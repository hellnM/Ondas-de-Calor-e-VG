# Ondas de Calor e Grafos de Visibilidade — Fortaleza (INMET A304)

Projeto computacional de análise de séries temporais climáticas usando redes de visibilidade **HVG** (*Horizontal Visibility Graph*) e **NVG** (*Natural Visibility Graph*). O objetivo é descrever a estrutura das redes construídas a partir da temperatura máxima diária da estação A304, em Fortaleza–CE, e investigar sua relação com os eventos de ondas de calor identificados pelos critérios **CTX90pct** e **EHF**.

## Notebook principal

O notebook [`ondas_de_calor_vg.ipynb`](ondas_de_calor_vg.ipynb) reúne as **Partes I, II e III** do trabalho, incluindo a preparação dos dados, a construção e a análise estrutural das redes, a análise de centralidade dos vértices e as verificações de robustez.

## Escopo da análise

- **Parte I — Dados e construção das redes:** preparação dos dados do INMET, diagnóstico de falhas, detecção/carregamento dos eventos de calor e construção dos grafos de visibilidade.
- **Parte II — Análise estrutural:** distribuição de graus, agrupamento, distâncias geodésicas, comparação com modelos de referência e teste da associação entre hubs e ondas de calor.
- **Parte III — Centralidade dos vértices:** métricas de centralidade, correlações entre métricas, relação com CTX90pct e EHF, análise de AUC, núcleos, assortatividade e alcance temporal.
- **Robustez:** comparação entre redes com empates e desempatadas, sensibilidade à interpolação, blocos contínuos sem interpolação e análise das métricas anuais.

## Recorte e correções relevantes

A análise principal utiliza **5.114 dias entre 2008 e 2021**. Foram identificados **13 dias sem temperatura máxima válida em dezembro de 2014**: o bloco de 04 a 15/12/2014 e o dia 17/12/2014, correspondendo a aproximadamente **0,254%** do recorte. A série principal utiliza interpolação linear, e a análise de robustez verifica separadamente o impacto desses valores imputados.

Como a temperatura tem resolução de 0,1 °C e contém valores repetidos, a análise principal emprega desempate com ruído gaussiano de desvio-padrão \(10^{-6}\) e semente 42. As redes com empates também são mantidas para comparação. No HVG, o número de arestas passa de 9.287 na versão com empates para 10.204 após o desempate.

## Resultados principais

Nas redes desempatadas, o HVG possui 5.114 nós e 10.204 arestas, e o NVG possui 5.114 nós e 17.278 arestas. Ambas as redes são conexas no recorte analisado. Os graus máximos são 31 no HVG e 134 no NVG.

O grau médio do HVG desempatado é 3,9906, próximo tanto ao valor de referência finito de 3,9929 quanto à média de 3,9931 obtida para séries embaralhadas (desvio-padrão 0,0013). Portanto, o grau médio isoladamente não permite distinguir a série observada de uma série embaralhada. A distribuição empírica de graus é próxima da distribuição teórica de referência, com a diferença mais evidente em \(k=2\).

A associação entre os hubs e os eventos de calor depende da rede utilizada. No HVG desempatado, 5,3% dos hubs coincidem com dias CTX90pct e 9,2% com dias EHF; esses resultados não diferem significativamente das taxas-base nos testes de deslocamento circular. No NVG, as proporções são 23,2% para CTX90pct e 25,0% para EHF, com evidência estatística de associação. Ainda assim, um hub não é, por si só, um indicador meteorológico de onda de calor: o maior hub, em 26/11/2013, não pertence a nenhum dos eventos considerados.

Na Parte III, as métricas de centralidade são avaliadas em relação aos eventos CTX90pct e EHF e comparadas com temperatura e anomalia sazonal. As métricas de centralidade, isoladamente, não superam essas referências. A análise de informação incremental é exploratória, pois as centralidades são calculadas sobre redes construídas a partir da série completa; uma avaliação prospectiva exigiria reconstruir as redes sem os anos usados para teste.

## Figuras

As células de visualização salvam as figuras em `data/plots/` no formato SVG. Entre os gráficos incluídos estão:

- distribuição de graus e validação de \(P(k)\);
- agrupamento em função do grau e comparação antes/depois do desempate;
- comparação de hubs e eventos de calor;
- correlações, distribuições e AUCs das centralidades;
- comparação entre série interpolada e blocos contínuos;
- efeito da interpolação sobre o grau do vértice de 02/12/2014;
- evolução anual das métricas do NVG;
- exemplos e visualizações topológicas de janelas temporais.

Os SVGs gerados pelo notebook já estão versionados em `data/plots/`. Para refazê-los, execute o notebook.

## Como executar

### Google Colab

1. Abra `ondas_de_calor_vg.ipynb` no Google Colab.
2. Em uma célula nova, antes de tudo, instale as bibliotecas que o Colab não traz: `!pip install -q ts2vg pymannkendall fastparquet`. Depois execute as células em ordem.
3. Se os arquivos processados forem solicitados, carregue os arquivos de dados correspondentes na estrutura `data/processed/` ou permita que as células de preparação os produzam quando o acesso à fonte estiver disponível.
4. Confira a saída das células e os SVGs gerados em `data/plots/`.

### Execução local

Requer Python 3.10 ou superior. Na raiz do repositório, instale as dependências listadas em `requirements.txt` e abra o notebook:

```bash
python -m venv .venv
source .venv/bin/activate       # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook ondas_de_calor_vg.ipynb
```

O notebook utiliza os diretórios relativos `data/raw`, `data/processed` e `data/plots`.

## Limitações

A análise cobre uma estação meteorológica e a temperatura máxima diária. Os resultados topológicos não estabelecem causalidade nem substituem a definição meteorológica de onda de calor. A interpolação pode alterar ligações locais, razão pela qual é examinada separadamente. Os resultados de AUC e de informação incremental não constituem validação prospectiva definitiva.

## Referências

- Lacasa, L. et al. “From time series to complex networks: The visibility graph.” *Proceedings of the National Academy of Sciences*, 105(13), 2008.
- Luque, B. et al. “Horizontal visibility graphs: Exact results for random time series.” *Physical Review E*, 80, 046103, 2009.
- Perkins, S. E., & Alexander, L. V. “On the measurement of heat waves.” *Journal of Climate*, 26(13), 4500–4517, 2013.

## Autoria

Hellen de Andrade Moura — Mestrado em Modelagem e Métodos Quantitativos, UFC.
