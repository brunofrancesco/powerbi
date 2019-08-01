# Fórmulas DAX de Inteligência Temporal
## Medida Exemplo
### Pode ser definida para Quantidade de Vendas, Receita, Matriculas e etc.

```dax
TotalMétrica = SUM(Valores[Métrica])
```

## Dia
### Legenda das Siglas: Day Over Day (DOD)
```dax
Métrica_DiaAnterior = CALCULATE([TotalMétrica],PREVIOUSDAY('dCalendario'[Data]))
Métrica_MesmoDiaAnoAnterior = CALCULATE([TotalMétrica],SAMEPERIODLASTYEAR('dCalendario'[Data]))
Métrica_DOD_Diferença = [TotalMétrica]-[Métrica_DiaAnterior]
Métrica_DOD_Variação = DIVIDE([Métrica_DOD_Diferença],[Métrica_DiaAnterior])
```

## Semana
### Legenda das Siglas: Week To Date (WTD); Week Over Week (WOW); Year Over Year (YOY)
```dax
Métrica_WTD = IF (HASONEVALUE ( 'dCalendario'[Ano] ) && HASONEVALUE ('dCalendario'[WeekNumber] ), CALCULATE( [TotalMétrica], FILTER ( ALL ( 'dCalendario' ), 'dCalendario'[Ano] = VALUES ( 'dCalendario'[Ano] ) && 'dCalendario'[WeekNumber] = VALUES ( 'dCalendario'[WeekNumber] ) && 'dCalendario'[Data] <= MAX ( 'dCalendario'[Data] ))))
Métrica_LastWeek = SUMX(FILTER(ALL('dCalendario'),IF(SELECTEDVALUE('dCalendario'[WeekNumber])=1,'dCalendario'[WeekNumber]=CALCULATE(MAX('dCalendario'[WeekNumber]), ALL('dCalendario')) && 'dCalendario'[Ano]=FORMAT(VALUE(SELECTEDVALUE('dCalendario'[Ano]))-1,""),'dCalendario'[WeekNumber]=SELECTEDVALUE('dCalendario'[WeekNumber])-1 && 'dCalendario'[Ano]=FORMAT(VALUE(SELECTEDVALUE('dCalendario'[Ano])),""))),[TotalMétrica])
Métrica_MesmaSemanaAnoAnterior = IF (HASONEVALUE ( 'dCalendario'[Ano] ) && HASONEVALUE ('dCalendario'[WeekNumber] ), CALCULATE( SUM ( Sales[Métrica] ), FILTER (ALL ( 'dCalendario' ),'dCalendario'[Ano] = FORMAT(VALUES ( 'dCalendario'[Ano] )-1,"") && 'dCalendario'[WeekNumber] = VALUES ( 'dCalendario'[WeekNumber] ) && 'dCalendario'[Data] <= MAX ( 'dCalendario'[Data] )))
Métrica_WTD_WOW_Diferença = [Métrica_WTD]-[Métrica_LastWeek]
Métrica_WTD_WOW_Variação = DIVIDE([Métrica_WTD_WOW_Diferença],[Métrica_LastWeek])
Métrica_WTD_YOY_Diferença = [Métrica_WTD]-[Métrica_MesmaSemanaAnoAnterior]
Métrica_WTD_YOY_Variação = DIVIDE([Métrica_WTD_YOY_Diferença],[Métrica_MesmaSemanaAnoAnterior])
```

## Mês
### Legenda das Siglas: Month To Date (MTD); Month Over Month (WOW); Year Over Year (YOY)
```dax
Métrica_MTD = TOTALMTD([TotalMétrica],'dCalendario'[Data])
Métrica_MesmoMesAnoAnterior = CALCULATE([Métrica_MTD],SAMEPERIODLASTYEAR('dCalendario'[Data]))
Métrica_MesAnterior = CALCULATE([TotalMétrica],PREVIOUSMONTH('dCalendario'[Data]))
Métrica_MTD_MOM_Diferença = [Métrica_MTD]-[Métrica_MesAnterior]
Métrica_MTD_MOM_Variação = DIVIDE([Métrica_MTD_MOM_Diferença],[Métrica_MesAnterior])
Métrica_MTD_YOY_Diferença = [Métrica_MTD]-[Métrica_MesmoMesAnoAnterior]
Métrica_MTD_YOY_Variação = DIVIDE([Métrica_MTD_YOY_Diferença],[Métrica_MesmoMesAnoAnterior])
```

## Ano
### Legenda das Siglas: Year To Date (YTD); Year Over Year (YOY)
```dax
Métrica_YOY_Diferença = [TotalMétrica]-[Métrica_MesmoDiaAnoAnterior]
Métrica_YOY_Variação = DIVIDE([Métrica_YOY_Diferença],[Métrica_LastDay])
Métrica_YTD = TOTALYTD([TotalMétrica],'dCalendario'[Data])
Métrica_AnoAnterior = CALCULATE([Métrica_YTD],PREVIOUSYEAR('dCalendario'[Data]))
Métrica_YTD_YOY_Diferença = [Métrica_YTD]-[Métrica_AnoAnterior]
Métrica_YTD_YOY_Variação = DIVIDE([Métrica_YTD_YOY_Diferença],[Métrica_AnoAnterior])
```

## Periodo
### Legenda das Siglas: Period To Date (PTD); Period Over Period (POP); Year Over Year (YOY)
```dax
Métrica_PTD = IF (HASONEVALUE ( 'dCalendario'[Ano] ) && HASONEVALUE ('dCalendario'[Bimestre] ),CALCULATE ([TotalMétrica],FILTER (ALL ( 'dCalendario' ),'dCalendario'[Ano] = VALUES ( 'dCalendario'[Ano] )&& 'dCalendario'[Bimestre] = VALUES ( 'dCalendario'[Bimestre] )&& 'dCalendario'[Data] <= MAX ( 'dCalendario'[Data] ))),BLANK ())
Métrica_MesmoPeriodoAnoAnterior = CALCULATE([Métrica_PTD],SAMEPERIODLASTYEAR('dCalendario'[Data]))
Métrica_PeriodoAnterior = CALCULATE([TotalMétrica],DATEADD('dCalendario'[Data],-2,MONTH))
Métrica_PTD_POP_Diferença = [Métrica_PTD]-[Métrica_PeriodoAnterior]
Métrica_PTD_POP_Variação = DIVIDE([Métrica_PTD_POP_Diferença],[Métrica_PeriodoAnterior])
Métrica_PTD_YOY_Diferença = [Métrica_PTD]-[Métrica_MesmoPeriodoAnoAnterior]
Métrica_PTD_YOY_Variação = DIVIDE([Métrica_PTD_YOY_Diferença],[Métrica_MesmoPeriodoAnoAnterior])
```

## Trimestre (ou Quartil:Q1, Q2, Q3, Q4)
### Legenda das Siglas: Quarter To Date (QTD); Quarter Over Quarter (QOQ); Year Over Year (YOY)
```dax
Métrica_QTD = TOTALQTD([TotalMétrica],'dCalendario'[Data])
Métrica_MesmoTrimestreAnoAnterior = CALCULATE([Métrica_QTD],SAMEPERIODLASTYEAR('dCalendario'[Data]))
Métrica_TrimestreAnterior = CALCULATE([TotalMétrica],PREVIOUSQUARTER('dCalendario'[Data]))
Métrica_QTD_QOQ_Diferença = [Métrica_QTD]-[Métrica_TrimestreAnterior]
Métrica_QTD_QOQ_Variação = DIVIDE([Métrica_QTD_QOQ_Diferença],[Métrica_TrimestreAnterior])
Métrica_QTD_YOY_Diferença = [Métrica_QTD]-[Métrica_MesmoTrimestreAnoAnterior]
Métrica_QTD_YOY_Variação = DIVIDE([Métrica_QTD_YOY_Diferença],[Métrica_MesmoTrimestreAnoAnterior])
```

