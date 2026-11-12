# XANDAR — CATÁLOGO DE TECNOLOGIAS E REPOSITÓRIOS
## Dancing with the SARs · Dançando com os SARs

**NASA Space Apps Challenge 2026 · Equipe Xandar · Vitória da Conquista, BA**  
**Versão:** 1.0 · **Pesquisa documental:** 10/11/2026  
**Finalidade:** apoiar brainstorming, avaliação de alternativas e escolha futura das ferramentas.

> **Este documento não define a arquitetura oficial da equipe.** É um catálogo colaborativo e não vinculante. O repositório de preparação reúne referências; o desenvolvimento da solução ficará no repositório técnico definido pelo líder da equipe. Esta pesquisa não é uma implementação antecipada do projeto nem uma autorização para desenvolver fora das regras do evento.

**O que esta versão entrega:** 62 repositórios catalogados, fontes de dados, serviços de hospedagem, opções de IA, quatro caminhos de arquitetura, critérios científicos e verificações para escolher uma combinação executável.

**Limite da pesquisa:** foram consultados documentação, páginas de produtos, repositórios e termos publicados. Não foram instaladas as bibliotecas, executados os notebooks, feitos downloads autenticados de produtos NISAR nem medidos desempenho, cobertura de uma área específica ou custo real. As combinações propostas precisam passar por um teste com os arquivos e as contas da equipe.

---

## Navegação

1. [Direção recomendada](#direcao)
2. [O que o desafio pede e o que falta confirmar](#desafio)
3. [Vocabulário essencial](#vocabulario)
4. [Qual produto NISAR utilizar](#produtos)
5. [Disponibilidade, maturidade e limitações atuais](#disponibilidade)
6. [Onde encontrar e obter os dados](#acesso)
7. [Dados complementares brasileiros e ópticos](#complementares)
8. [Catálogo de 62 repositórios](#repositorios)
9. [Ferramentas de IA e seus limites](#ia)
10. [Hospedagem, banco, armazenamento e VPS](#infraestrutura)
11. [Quatro arquiteturas possíveis](#arquiteturas)
12. [Ferramentas por fenômeno estudado](#fenomenos)
13. [Método científico, matemática e validação](#validacao)
14. [Experiência do usuário e demonstração](#interface)
15. [O que não adotar automaticamente](#nao-adotar)
16. [Licenças, segurança e rastreabilidade](#licencas)
17. [Roteiro de decisão e plano de contingência](#roteiro)
18. [Como contribuir para este catálogo](#contribuir)
19. [Referências técnicas prioritárias](#referencias)

---

<a id="direcao"></a>
## 1. Direção recomendada

**Minha recomendação inicial para a Xandar é construir um explorador de mudanças em uma área delimitada, com poucas datas comparáveis e resultados pré-processados.** Trata-se de uma proposta de escopo, não de uma decisão já tomada.

Antes de acrescentar inteligência artificial, login, banco ou VPS, o protótipo deve demonstrar uma sequência compreensível:

**observações NISAR reais → preparação documentada → comparação válida → mapa interativo → explicação das mudanças e das limitações.**

### Lista curta para começar a avaliação

| Necessidade                                        | Candidato inicial                             | Alternativa                                |
| -------------------------------------------------- | --------------------------------------------- | ------------------------------------------ |
| Entender o dado e os exemplos                      | NISAR User Guide + NISAR Cookbook             | Tutoriais específicos do ASF               |
| Encontrar arquivos da área                         | ASF Vertex + earthaccess                      | asf_search                                 |
| Escolher produto para mudança de retroespalhamento | GCOV                                          | Outro produto somente se o fenômeno exigir |
| Ler HDF5 e organizar matrizes                      | h5py + NumPy                                  | xarray, com leitura/grupos apropriados     |
| Recortar e gerar arquivos geográficos              | Rasterio/GDAL                                 | openSEPPO                                  |
| Inspecionar o mapa manualmente                     | QGIS                                          | SNAP com leitor NISAR compatível           |
| Criar composição de falsas cores                   | nisar-py                                      | Visualização própria documentada           |
| Primeira interface em Python                       | Streamlit + Plotly + mapa compatível          | Frontend próprio                           |
| Interface web mais personalizada                   | React + Vite + OpenLayers                     | MapLibre + camadas/tiles preparados        |
| Publicar um estudo pré-calculado                   | Cloudflare Pages ou Streamlit Community Cloud | Vercel, se o uso atender ao Hobby          |
| Guardar estudos e resultados de usuários           | Supabase, apenas se necessário                | JSON/CSV/Parquet ou DuckDB no protótipo    |
| Registrar evidências e apresentar                  | JupyterLab + pytest + OBS                     | Complementar com Kdenlive                  |

Os recursos, links e condições de cada candidato estão detalhados nas seções seguintes. As ferramentas da mesma função são alternativas: **não é necessário instalar todas**.

**Itens que eu deixaria fora da primeira versão, salvo necessidade demonstrada:** cadastro de usuários, processamento global sob demanda, treinamento de modelo fundacional, servidor GPU permanente, banco de vetores, microsserviços e análise InSAR complexa.

---

<a id="desafio"></a>
## 2. O que o desafio pede e o que falta confirmar

A reprodução do enunciado pelo organizador local **HackAthlone/TUS** descreve uma aplicação interativa que utiliza NISAR para acompanhar e comunicar mudanças da superfície. Sugere escolher uma área, explorar datas/polarizações e contextualizar com dados auxiliares. A apresentação pode explorar mapas, séries temporais e narrativa científica.

**Disponibilidade da fonte:** a página global oficial retornou erro 502 nesta pesquisa. O texto foi conferido na reprodução do organizador, que ressalva que a página oficial prevalece e informa recursos previstos para **13/11/2026**. Conferir novamente os requisitos e materiais oficiais antes da submissão.

Fontes: [enunciado reproduzido por HackAthlone](https://hackathlone.com/challenges/dancing-with-the-sars) · [página global a reconferir](https://www.spaceappschallenge.org/2026/challenges/dancing-with-the-sars/).

**Interpretação de escopo para nossa equipe:** não precisamos solucionar todos os fenômenos nem atender qualquer lugar do planeta na primeira versão. Uma pergunta clara, uma área bem documentada e uma comparação defensável são um ponto de partida mais controlável.

---

<a id="vocabulario"></a>
## 3. Vocabulário essencial

| Termo | Significado para este projeto |
|---|---|
| **SAR** | Radar de abertura sintética: mede o retorno de sinais de micro-ondas; não é uma fotografia óptica. |
| **NISAR** | Missão conjunta NASA–ISRO; é a fonte de radar central deste desafio. |
| **Retroespalhamento** | Sinal devolvido em direção ao radar. Sua variação pode estar associada a diferentes propriedades da superfície, não a uma causa única. |
| **Polarização** | Configuração de transmissão/recepção, como HH ou HV. Comparações precisam respeitar os canais disponíveis. |
| **GCOV** | Produto geocodificado de covariância polarimétrica. Um ponto de partida para explorar intensidade e mudanças. |
| **InSAR** | Uso de informação interferométrica de fase; é diferente de subtrair imagens de intensidade. |
| **HDF5** | Contêiner hierárquico com matrizes e metadados. Um arquivo pode conter muitas variáveis. |
| **COG** | Cloud-Optimized GeoTIFF: GeoTIFF organizado para acesso eficiente, inclusive por partes, quando o servidor e o cliente suportam. |
| **AOI** | Área de interesse; o polígono ou recorte que vamos estudar. |
| **CRS** | Sistema de referência de coordenadas; determina como uma posição se relaciona ao mapa. |
| **NoData / máscara** | Identificadores de pixels sem observação válida ou que devem ser excluídos. Ausência de dado não significa ausência de mudança. |
| **CRID** | Identificação da versão de processamento NISAR; importante para rastrear diferenças entre produtos. |
| **Tiles** | Pequenos blocos usados para montar um mapa no navegador. |
| **Backend / VPS** | Backend é a lógica no servidor; VPS é uma máquina virtual administrável. Uma aplicação pode precisar de nenhum dos dois. |

Referências: [guia NISAR](https://nisar-docs.asf.alaska.edu/), [formato dos dados](https://nisar-docs.asf.alaska.edu/data-format/), [GCOV](https://nisar-docs.asf.alaska.edu/gcov/), [exemplo COG do OpenLayers](https://openlayers.org/en/latest/examples/cog.html).

---

<a id="produtos"></a>
## 4. Qual produto NISAR utilizar

**Não começar pelo arquivo mais bruto apenas porque ele contém mais informação.** O produto deve atender à pergunta científica.

| Produto | Uso possível | Decisão sugerida para a Xandar |
|---|---|---|
| **L2 GCOV** | Covariância/retroespalhamento geocodificado e com correções radiométricas de terreno. | **Primeiro candidato** para água, vegetação, agricultura e comparação visual de mudanças. Os termos diagonais são medidas de potência; observar canais e unidades. |
| **L2 GUNW** | Fase interferométrica desembrulhada e camadas de qualidade. | Considerar para deformação/subsidência com conhecimento específico. Não equivale a um mapa pronto de deslocamento tridimensional. |
| **L2 GOFF** | Deslocamentos por rastreamento de pixels e indicadores associados. | Considerar para movimento de geleiras ou superfícies com deslocamentos rastreáveis, respeitando incerteza e geometria. |
| **GSLC / RSLC** | Dados complexos que permitem análises SAR mais avançadas. | Reservar para necessidade científica explícita e equipe capaz de executar o processamento. |
| **L3 SME2** | Estimativa geofísica de umidade do solo. | Conferir maturidade e limitações específicas; não confundir intensidade GCOV com umidade absoluta medida. |

Fontes por produto: [GCOV](https://nisar-docs.asf.alaska.edu/gcov/), [GUNW](https://nisar-docs.asf.alaska.edu/gunw/), [GOFF](https://nisar-docs.asf.alaska.edu/goff/), [visão geral dos produtos](https://nisar-docs.asf.alaska.edu/products-overview/), [disponibilidade do SME2](https://nisar-docs.asf.alaska.edu/availability-overview/).

**Cuidado de leitura:** os nomes internos podem incluir `HHHH`, `HVHV` e outros termos de covariância. Não fixar caminhos de bandas por suposição. A documentação de GCOV informa espaçamentos de pixels de 10 ou 20 metros conforme o modo; **espaçamento da grade não deve ser apresentado automaticamente como resolução espacial efetiva**. [Fonte](https://nisar-docs.asf.alaska.edu/gcov/).

---

<a id="disponibilidade"></a>
## 5. Disponibilidade, maturidade e limitações atuais

### 5.1 Fotografia do acervo consultado em 10/11/2026

Segundo o ASF:

- Produtos L0–L2 **VALIDATED** foram liberados em **06/11/2026**, abrangendo aquisições desde **21/10/2026**.
- Produtos **PROVISIONAL** abrangem um período anterior; misturá-los com outras maturidades/versões exige cuidado.
- Há uma lacuna instrumental permanente de **27/07/2026 22:03:25 UTC a 10/08/2026 00:55:27 UTC**.
- **SME2 VALIDATED ainda não estava disponível**.
- O reprocessamento validado de dados anteriores está planejado, não deve ser tratado como já concluído.

**Consequência prática:** pesquisar as datas existentes antes de escolher o evento. Não prometer uma análise NISAR de um acontecimento ocorrido antes da missão, nem uma série longa de produtos validados sem conferir o acervo.

Fonte: [Available NISAR Data](https://nisar-docs.asf.alaska.edu/availability-overview/).

### 5.2 Serviços de recorte: há divergência na documentação

A página do **Harmony** descreve extração de variáveis, recorte e reprojeção para vários produtos. Porém, o **roadmap do ASF** ainda classifica parte dessas funções como pendentes, incluindo recorte espacial/reprojeção de GCOV, e outros produtos sem calendário.

**Decisão prudente:** usar extração de camadas GCOV como funcionalidade documentada, mas confirmar na coleção real cada operação adicional. Manter conversão/recorte local como alternativa. Não tratar funcionalidades planejadas de Harmony ou HyP3 como garantidas.

Fontes: [Harmony](https://nisar-docs.asf.alaska.edu/harmony/) · [roadmap](https://nisar-docs.asf.alaska.edu/roadmap/).

### 5.3 Atualização das ferramentas também importa

O Cookbook declara desenvolvimento ativo e adaptação aos produtos PROVISIONAL. Para qualquer exemplo, registrar versão do código e maturidade do arquivo utilizado. Uma mudança de formato ou processamento pode exigir ajuste no notebook. [Fonte](https://github.com/ASFOpenSARlab/NISAR_Cookbook).

O SNAP 14 documenta leitura de produtos NISAR. Isso **não autoriza repetir automaticamente operadores de calibração ou todo um fluxo feito para Sentinel-1**; a documentação do leitor, por exemplo, distingue GCOV já calibrado. [Fonte](https://step.esa.int/main/wp-content/help/versions/14.0.0/snap-toolboxes/eu.esa.microwavetbx.sar.io/readers/ImportNisar.html).

---

<a id="acesso"></a>
## 6. Onde encontrar e obter os dados

| Fonte / ferramenta | Papel no projeto | Condição e cuidado |
|---|---|---|
| [ASF Vertex](https://search.asf.alaska.edu/) | Verificar cobertura e descobrir produtos NISAR por área, data e tipo. | A interface ajuda a selecionar; o download pode exigir Earthdata Login. |
| [Earthdata Search](https://search.earthdata.nasa.gov/) | Localizar coleções e granules NASA. | Conferir identificação e maturidade da coleção; ver [guia NISAR](https://nisar-docs.asf.alaska.edu/earthdata-search/). |
| [Earthdata Login](https://urs.earthdata.nasa.gov/) | Autenticação para acesso aos dados. | Conta individual, credenciais fora do Git. Acesso não equivale a publicar tokens para os usuários da aplicação. |
| [earthaccess — guia NISAR](https://nisar-docs.asf.alaska.edu/earthaccess/) | Busca e download pelo Python. | Validar filtros e retorno; armazenar identificadores dos arquivos selecionados. |
| [CMR API](https://cmr.earthdata.nasa.gov/search/site/docs/search/api.html) | Consulta programática de catálogo e metadados. | Mais controle, mas mais integração; dispensável se earthaccess já resolver. |
| [Harmony](https://nisar-docs.asf.alaska.edu/harmony/) | Solicitar transformações de dados no serviço NASA. | Conferir a divergência de disponibilidade descrita na seção 5.2. |
| [NASA Worldview](https://worldview.earthdata.nasa.gov/) | Inspeção visual e contexto das observações. | RGB de visualização não substitui medidas originais; conferir a maturidade da camada. [Guia](https://nisar-docs.asf.alaska.edu/tools-services-overview/). |
| [Bhoonidhi / ISRO](https://bhoonidhi.nrsc.gov.in/NISAR/) | Investigar produtos NISAR da banda S. | Verificar acesso, cobertura e documentação próprios; não assumir a mesma oferta da banda L no ASF. |
| [OpenSARLab / Cookbook](https://asfopensarlab.github.io/NISAR_Cookbook/) | Ambiente e materiais de aprendizado SAR. | Acesso ao ambiente hospedado pode depender de cadastro/liberação; manter execução local como opção. |
| [HyP3](https://hyp3-docs.asf.alaska.edu/) | Processamento SAR sob demanda para produtos suportados. | Basic e serviço pago têm regras próprias. O [roadmap NISAR](https://nisar-docs.asf.alaska.edu/roadmap/) não garante geração personalizada de GUNW já operacional. |

**Não é obrigatório contratar AWS para baixar por HTTPS.** Acesso direto a S3 pode exigir credenciais temporárias e região/ambiente compatíveis; não desenhar o protótipo dependendo de leitura S3 irrestrita a partir de qualquer computador. [Tutorial de acesso NISAR do MAAP](https://docs.maap-project.org/en/develop/science/NISAR/NISAR_access.html).

**Saída esperada desta etapa:** uma lista pequena de arquivos reais, com coleção, identificador, data/hora, produto, polarização, órbita/track/frame, maturidade, versão de processamento, área coberta e tamanho.

---

<a id="complementares"></a>
## 7. Dados complementares brasileiros e ópticos

**O objetivo é contextualizar ou confrontar resultados, não substituir o dado NISAR central do desafio.** Abaixo estão usos propostos pela pesquisa, sujeitos à adequação do conjunto escolhido.

| Fonte | O que pode acrescentar | Cuidado de interpretação |
|---|---|---|
| [IBGE — malhas territoriais](https://www.ibge.gov.br/geociencias/organizacao-do-territorio/malhas-territoriais/15774-malhas.html) / geobr | Municípios e recortes administrativos para resumir áreas alteradas. | Informar ano e escala. Limite administrativo não valida sozinho uma mudança ambiental. |
| [INPE TerraBrasilis](https://terrabrasilis.dpi.inpe.br/downloads/) | Dados de PRODES/DETER para análises de vegetação e desmatamento. | Produtos têm metodologias e calendários distintos; não usar alerta como equivalente automático à classe prevista pelo nosso algoritmo. |
| [MapBiomas](https://brasil.mapbiomas.org/downloads/) | Uso e cobertura da terra; contexto de floresta, água e agricultura. | Consultar coleção, legenda, resolução, termos e referência. Mapa anual não informa necessariamente a data exata de um evento. |
| [ANA — dados abertos](https://dadosabertos.ana.gov.br/) | Bacias e informações hidrológicas para hipóteses sobre água/inundação. | O acesso à [API HidroWeb](https://www.snirh.gov.br/hidroweb/acesso-api) tem procedimento próprio; não presumir acesso imediato sem autorização. |
| [INMET — dados históricos](https://portal.inmet.gov.br/dadoshistoricos/) | Chuva e outras observações meteorológicas para contextualizar mudanças. | Conferir estação, horário, unidade, dados faltantes e distância até a área estudada. |
| [NASA HLS](https://hls.gsfc.nasa.gov/data-access-and-tools/) | Imagens ópticas Landsat/Sentinel-2 harmonizadas, com tutoriais no catálogo de repositórios. | Usar máscaras de nuvens e compatibilizar datas/grade. Imagem óptica é evidência complementar, não validação perfeita. |
| [OpenStreetMap](https://www.openstreetmap.org/copyright) | Vias e feições para contexto do mapa. | Código do mapa, licença dos dados e serviço de tiles são coisas distintas. Não estimar vítimas ou danos reais apenas pela sobreposição. |

**Seleção sugerida:** começar com NISAR + um conjunto territorial + uma fonte independente ligada ao fenômeno. Acrescentar fontes apenas quando respondam a uma pergunta concreta.

---

<a id="repositorios"></a>
## 8. Catálogo de 62 repositórios

### Como ler

**P1:** candidato prioritário de avaliação. **P2:** depende da funcionalidade. **P3:** avançado ou reservado para expansão. As prioridades e dificuldades são avaliações editoriais para este protótipo, não garantias de tempo de execução.

As licenças indicadas são as principais informadas nas páginas/arquivos consultados. Conferir o `LICENSE` da revisão efetivamente usada e os avisos de componentes. Licença do código não cobre automaticamente pesos de IA, datasets, mapas-base ou hospedagem.

**Custo de referência:** o código aberto pode ser usado conforme a licença sem uma assinatura do serviço proprietário; execução, equipamento, servidores e APIs continuam podendo ter custo. Cada nome abaixo aponta para a fonte primária do respectivo repositório.

### 8.1. NISAR: acesso, conversão e análise especializada

| ID / repositório | Aplicação específica | Licença principal | Prioridade / dificuldade | Limite ou cuidado |
|---|---|---|---|---|
| **R01 · [NISAR Cookbook — ASF](https://github.com/ASFOpenSARlab/NISAR_Cookbook)** | Ponto de partida para aprender a buscar, abrir e analisar produtos NISAR em notebooks. Usar os exemplos como referência para o fluxo científico. | Apache-2.0 | P1 · média | Em desenvolvimento e em adaptação aos produtos PROVISIONAL. Conferir cada notebook, dependências e maturidade do dado; nem todo exemplo está pronto. |
| **R02 · [earthaccess](https://github.com/earthaccess-dev/earthaccess)** | Pesquisar coleções NASA, autenticar com Earthdata e obter granules. Exemplo: localizar aquisições para a área e o intervalo escolhidos. | MIT | P1 · baixa/média | Não publicar credenciais. Conferir coleção, produto, datas e cobertura real; resultado de busca não garante um par comparável. |
| **R03 · [asf_search](https://github.com/asfadmin/Discovery-asf_search)** | Pesquisar o acervo SAR do ASF por área, tempo e atributos. Alternativa ou complemento ao earthaccess para encontrar os arquivos do estudo. | BSD-3-Clause | P1 · baixa/média | Conferir os filtros aceitos para NISAR. Download autenticado e identificadores não são equivalentes a um servidor público de mapas. |
| **R04 · [nisar-py — ASFHyP3](https://github.com/ASFHyP3/nisar-py)** | Converter GCOV em composição RGB de falsas cores para visualização. Útil para o mapa de antes/depois e a narrativa visual. | BSD-3-Clause | P1 · média | O módulo gcov_rgb gera COG sem overviews; considerar criá-las. RGB é derivado visual: conservar os valores científicos separados. Cores diferem entre produtos single-pol e dual-pol. |
| **R05 · [openSEPPO](https://github.com/EarthBigData/openSEPPO)** | Conversão e organização de dados SAR, com ferramentas NISAR GCOV para GeoTIFF/COG, recortes e outros tratamentos. | Apache-2.0 | P1 · média | Avaliar como alternativa de conversão, não adicionar junto de todos os conversores. Registrar opções que mudam projeção, resolução ou grandeza radiométrica. |
| **R06 · [nisar_pytools](https://github.com/ZachHoppinen/nisar_pytools)** | Explorar NISAR com Python e estruturas xarray; útil para acesso a matrizes e operações especializadas. | MIT | P2 · média/alta | Biblioteca especializada: testar com o produto e a versão escolhidos. Recursos ligados a processamento ISCE aumentam as dependências. |
| **R07 · [NISAR QA](https://github.com/isce-framework/nisarqa)** | Verificar metadados e gerar métricas e relatórios de qualidade do produto, antes de interpretar diferenças como mudanças reais. | BSD-3-Clause OU Apache-2.0 | P2 · média/alta | Usar o ambiente recomendado. Relatório de qualidade do arquivo não comprova a classificação ambiental nem a causa de uma mudança. |
| **R08 · [ISCE3](https://github.com/isce-framework/isce3)** | Processamento SAR de baixo nível e componentes utilizados nos fluxos NISAR. Opção para quem precisar controlar etapas científicas avançadas. | Apache-2.0; ver avisos do LICENSE | P3 · alta | Não é a primeira escolha para apenas mostrar GCOV. Instalação, processamento e dependências podem exceder o escopo do protótipo. |
| **R09 · [MintPy](https://github.com/insarlab/MintPy)** | Analisar séries InSAR de deslocamento na direção de visada. Candidato para subsidência ou deformação, com produtos e preparação compatíveis. | GPL-3.0; conferir componentes no LICENSE | P3 · alta | Validar suporte do prep_nisar à versão dos dados. Exige correções, referências e controle de qualidade; não transforma intensidade GCOV em deslocamento. |
| **R10 · [PolSARtools](https://github.com/polsartools/polsartools)** | Análises polarimétricas e decomposições SAR, incluindo fluxos NISAR. Pode ajudar a explorar diferenças de estrutura e espalhamento. | GPL-3.0 | P3 · alta | Cada algoritmo exige canais específicos. Não presumir que a área escolhida tenha todas as polarizações ou produtos quad-pol. |
| **R11 · [SNAP — Microwave Toolbox](https://github.com/senbox-org/microwave-toolbox)** | Ambiente ESA para importar e examinar dados SAR. A documentação do SNAP 14 descreve leitura de produtos NISAR, incluindo GCOV. | GPL-3.0 | P2 · média/alta | Conferir a versão do leitor e cada operador. GCOV já é calibrado; não repetir calibração genérica nem assumir que qualquer fluxo Sentinel-1 serve para NISAR. |

### 8.2. Base científica, geoprocessamento e matemática

| ID / repositório | Aplicação específica | Licença principal | Prioridade / dificuldade | Limite ou cuidado |
|---|---|---|---|---|
| **R12 · [h5py](https://github.com/h5py/h5py)** | Inspecionar grupos, atributos e matrizes do HDF5 NISAR; ler apenas a variável ou janela necessária. | BSD-3-Clause | P1 · média | Uma matriz NumPy sem coordenadas não vira um mapa correto. Preservar projeção, orientação, escala, unidade e máscaras. |
| **R13 · [xarray](https://github.com/pydata/xarray)** | Organizar cubos com dimensões de tempo, posição e polarização. Exemplo: calcular uma série temporal por área. | Apache-2.0 | P1 · média | HDF5 é hierárquico: conferir grupo e engine. Alinhar coordenadas e metadados antes de comparar datas. |
| **R14 · [rioxarray](https://github.com/vylor/rioxarray)** | Acrescentar operações geográficas a xarray, como recorte, CRS, exportação e reprojeção. | Apache-2.0; avisos de componentes | P2 · média | O endereço corteva/rioxarray redirecionou para vylor/rioxarray na consulta. Fixar versão e usar a documentação correspondente. |
| **R15 · [Rasterio](https://github.com/rasterio/rasterio)** | Ler/escrever rasters, trabalhar por janelas, aplicar máscaras e exportar os recortes para GeoTIFF. | BSD-3-Clause | P1 · média | Leitura de HDF5 depende do GDAL e dos drivers instalados. Usar um dataset interno apropriado, não presumir que qualquer HDF5 abre diretamente. |
| **R16 · [GDAL](https://github.com/OSGeo/gdal)** | Converter formatos, reprojetar e produzir GeoTIFF/COG com overviews. É uma base importante para preparar arquivos leves para a web. | MIT e avisos de componentes | P1 · média | Compatibilidade NISAR depende da versão e dos drivers. Não instalar combinações incompatíveis de bibliotecas binárias. |
| **R17 · [QGIS](https://github.com/QGIS/QGIS)** | Inspecionar camadas visualmente, conferir georreferenciamento e preparar mapas sem escrever todo o processamento em código. | GPL-2.0; verificar a versão distribuída | P1 · baixa/média | Usar a documentação oficial para conferir suporte ao formato. Plugins e camadas de terceiros têm condições próprias. |
| **R18 · [NumPy](https://github.com/numpy/numpy)** | Cálculos matriciais, diferenças e razões entre imagens, percentis e máscaras numéricas. | BSD-3-Clause | P1 · média | Definir a grandeza física antes da operação; tratar zeros, NoData, valores negativos inválidos e divisão por zero. |
| **R19 · [SciPy](https://github.com/scipy/scipy)** | Estatística, filtros e operações numéricas para explorar mudanças e sua variabilidade. | BSD-3-Clause | P2 · média | Um filtro genérico não é automaticamente apropriado para speckle SAR; justificar e avaliar seu efeito. |
| **R20 · [scikit-image](https://github.com/scikit-image/scikit-image)** | Segmentação, limiarização e operações morfológicas. Exemplo: testar uma máscara de mudança como linha de base. | BSD-3-Clause; conferir componentes | P2 · média | Limiares automáticos não identificam, sozinhos, a causa física. Validar em áreas com referência independente. |
| **R21 · [GeoPandas](https://github.com/geopandas/geopandas)** | Cruzar polígonos de mudança com limites municipais, bacias e áreas de interesse. | BSD-3-Clause | P1 · média | Conferir CRS e validade das geometrias. Para áreas, usar cálculo geodésico ou projeção adequada, não graus quadrados. |
| **R22 · [pyproj](https://github.com/pyproj4/pyproj)** | Transformar coordenadas e calcular distâncias e áreas geodésicas. | MIT | P2 · média | Verificar ordem longitude/latitude, datum e unidade; uma transformação incorreta desloca a camada no mapa. |
| **R23 · [Dask](https://github.com/dask/dask)** | Executar cálculos em blocos e integrar processamento lazy com xarray quando o recorte cresce. | BSD-3-Clause | P2 · média/alta | Não cria RAM nem GPU gratuitas. Dimensionar chunks e medir uso de memória antes de adotar processamento distribuído. |
| **R24 · [ruptures](https://github.com/deepcharles/ruptures)** | Investigar pontos de mudança em séries temporais de indicadores extraídos do radar. | BSD-2-Clause | P2 · média | Precisa de série suficiente e comparável. Não faz sentido prometer tendência ou ruptura robusta com duas imagens. |
| **R25 · [geobr](https://github.com/ipea/geobr)** | Obter geometrias brasileiras para contextualizar os resultados por município, estado ou outros recortes disponíveis. | MIT para o código; dados com fontes próprias | P2 · baixa/média | Escolher ano e escala apropriados; citar o produtor original do dado, não apenas a biblioteca. |
| **R26 · [HLS Data Resources — NASA](https://github.com/nasa/HLS-Data-Resources)** | Tutoriais para acessar imagens ópticas Harmonized Landsat Sentinel-2 e comparar o contexto visual com as mudanças no radar. | Apache-2.0 | P2 · média | Fonte complementar, não substituta do NISAR. Usar máscaras de nuvem, datas e resolução adequadas e não tratar imagem óptica como verdade de campo automática. |

### 8.3. Mapas, interface, gráficos e publicação de rasters

| ID / repositório | Aplicação específica | Licença principal | Prioridade / dificuldade | Limite ou cuidado |
|---|---|---|---|---|
| **R27 · [Streamlit](https://github.com/streamlit/streamlit)** | Construir uma aplicação Python com seleção de datas, gráficos e indicadores. Caminho rápido para a primeira demonstração. | Apache-2.0 | P1 · baixa/média | Pré-calcular as etapas caras e controlar reruns/cache. A biblioteca é aberta; hospedagem e seus limites são separados. |
| **R28 · [Folium](https://github.com/python-visualization/folium)** | Criar mapas Leaflet pelo Python com overlays e polígonos, úteis em notebooks ou páginas geradas. | MIT | P1 · baixa/média | HDF5 não é uma camada pronta. Preparar imagens georreferenciadas ou tiles; callbacks interativos podem exigir integração adicional. |
| **R29 · [leafmap](https://github.com/opengeos/leafmap)** | Explorar dados geoespaciais em Python e montar visualizações com diferentes backends de mapas. | MIT | P2 · média | Um widget de notebook não funciona automaticamente no Streamlit. Escolher e testar o backend compatível com a interface. |
| **R30 · [Plotly.py](https://github.com/plotly/plotly.py)** | Gráficos de séries temporais, distribuição de valores e área sinalizada como alterada. | MIT | P1 · baixa/média | Eixos e legendas devem indicar unidade, datas e quantidade de pixels válidos. Interatividade não corrige uma análise inválida. |
| **R31 · [OpenLayers](https://github.com/openlayers/openlayers)** | Mapa web com suporte a GeoTIFF/COG, reprojeções e camadas. Boa opção para consultar recortes COG no navegador. | BSD-2-Clause | P1 · média | Validar CORS, Range requests, projeção e desempenho no celular. Leitura no navegador não significa que qualquer arquivo enorme seja viável. |
| **R32 · [MapLibre GL JS](https://github.com/maplibre/maplibre-gl-js)** | Mapa vetorial e raster com interface moderna; bom candidato para uma aplicação React com linha do tempo. | BSD-3-Clause | P1 · média | Não é um leitor universal de HDF5/COG. Definir adaptação, imagens georreferenciadas ou serviço de tiles. Mapa-base tem licença e custo próprios. |
| **R33 · [maplibre-gl-compare](https://github.com/maplibre/maplibre-gl-compare)** | Comparador deslizante entre dois mapas MapLibre: adequado para visualizar antes e depois. | ISC | P2 · baixa/média | Sincronizar enquadramento, escala de cores e legenda. A comparação visual precisa usar dados comparáveis. |
| **R34 · [Leaflet](https://github.com/Leaflet/Leaflet)** | Alternativa leve para mapas web, overlays e seleção de áreas, especialmente quando não é necessário um mapa vetorial complexo. | BSD-2-Clause | P2 · baixa/média | Escolher entre ele, OpenLayers e MapLibre, em vez de instalar todos. Plugins variam em licença e manutenção. |
| **R35 · [geotiff.js](https://github.com/geotiffjs/geotiff.js)** | Ler GeoTIFF no JavaScript e acessar bandas/pixels; útil para consultas numéricas e integrações específicas. | MIT | P2 · média | Não processa NISAR HDF5 diretamente. Arquivos, codecs e amostragem precisam de teste no navegador alvo. |
| **R36 · [TiTiler](https://github.com/developmentseed/titiler)** | Servir tiles e operações sobre rasters por API, para mapas dinâmicos a partir de COGs. | MIT | P2 · média/alta | Exige hospedagem. Usar lista autorizada de URLs e limites de requisição; não depender de um servidor público de demonstração. |
| **R37 · [rio-tiler](https://github.com/cogeotiff/rio-tiler)** | Biblioteca para obter tiles, recortes e estatísticas de rasters; base para um backend geoespacial customizado. | BSD-3-Clause | P2 · média | Evitar reimplementar o TiTiler sem necessidade. Leitura repetida de arquivos remotos pode aumentar latência e operações de armazenamento. |
| **R38 · [PMTiles](https://github.com/protomaps/PMTiles)** | Distribuir um arquivo de tiles pré-gerados com acesso por intervalos HTTP, reduzindo a necessidade de um servidor de mapas. | BSD-3-Clause no código; especificação com termos próprios | P2 · média | Precisa de geração de tiles, hospedagem compatível e atribuição das fontes. Tiles de visualização não substituem o raster científico original. |
| **R39 · [deck.gl](https://github.com/visgl/deck.gl)** | Renderizar grandes conjuntos de polígonos, pontos e camadas animadas com GPU no navegador. | MIT | P3 · média/alta | Só acrescentar se o mapa escolhido não resolver o caso. Medir custo de GPU, memória e uso em aparelhos modestos. |
| **R40 · [React](https://github.com/react/react)** | Organizar a interface em componentes: seleção de estudo, mapa, gráficos, legenda e painel de fontes. | MIT | P1 · média | Alternativa ao caminho Streamlit, não requisito adicional. O endereço facebook/react redirecionou para react/react na consulta. |
| **R41 · [Vite](https://github.com/vitejs/vite)** | Ambiente de desenvolvimento e build do frontend React ou JavaScript para publicação estática. | MIT | P1 · baixa/média | Variáveis expostas no build do frontend não guardam segredos. Configurar caminhos e carregamento de assets no provedor escolhido. |

### 8.4. IA e aprendizado de máquina: apenas quando justificáveis

| ID / repositório | Aplicação específica | Licença principal | Prioridade / dificuldade | Limite ou cuidado |
|---|---|---|---|---|
| **R42 · [scikit-learn](https://github.com/scikit-learn/scikit-learn)** | Classificadores e métricas para testar uma linha de base com atributos do radar e referências rotuladas. | BSD-3-Clause | P2 · média | Separar treino e teste por regiões/datas para reduzir vazamento espacial e temporal; não confundir boa métrica interna com generalização. |
| **R43 · [TorchGeo](https://github.com/torchgeo/torchgeo)** | Infraestrutura de aprendizado profundo geoespacial, amostragem e integração de dados. | MIT no código; modelos/datasets separados | P3 · alta | Confirmar o dataset, as bandas e o modelo suportados. A biblioteca não garante um classificador pronto de mudanças NISAR. |
| **R44 · [TerraTorch](https://github.com/torchgeo/terratorch)** | Ajustar e avaliar modelos fundacionais geoespaciais, quando houver dados, rótulos e infraestrutura. | Apache-2.0; arquivos MIT identificados no projeto | P3 · alta | O antigo endereço IBM/terratorch redirecionou. Conferir licença dos pesos e não planejar treinamento pesado como dependência da demo. |
| **R45 · [TerraMind](https://github.com/IBM/terramind)** | Investigar modelos multimodais de observação da Terra e fusão de modalidades como pesquisa adicional. | Apache-2.0 no repositório; conferir checkpoints | P3 · alta | Experiência com Sentinel-1 não prova compatibilidade com NISAR. Modalidade gerada pelo modelo não pode ser apresentada como observação medida. |
| **R46 · [Prithvi-EO-2.0](https://github.com/NASA-IMPACT/Prithvi-EO-2.0)** | Explorar análise óptica complementar com modelo associado a dados HLS, quando isso ajudar a contextualizar o radar. | MIT no repositório; pesos com ficha própria | P3 · alta | Não é um modelo NISAR pronto. Não alimentar bandas de radar como se fossem as bandas ópticas de treinamento. |
| **R47 · [Ollama](https://github.com/ollama/ollama)** | Executar um modelo local para apoiar redação, explicações e revisão de código sem depender de uma API paga. | MIT na ferramenta; licença por modelo | P2 · média | Exige hardware compatível. Não é um detector científico SAR; conferir a licença do modelo e distinguir execução local de serviços em nuvem. |

### 8.5. Backend, dados, ambientes e controle de qualidade

| ID / repositório | Aplicação específica | Licença principal | Prioridade / dificuldade | Limite ou cuidado |
|---|---|---|---|---|
| **R48 · [FastAPI](https://github.com/fastapi/fastapi)** | API para consultas por área, estatísticas e catálogo de resultados já processados. | MIT | P2 · média | Desnecessária se uma aplicação estática resolver. Não disparar processamento de cenas inteiras a cada acesso. |
| **R49 · [Supabase](https://github.com/supabase/supabase)** | Guardar metadados, estudos, polígonos de interesse e registros salvos, com Postgres e autenticação quando necessária. | Apache-2.0 no repositório; componentes/serviço separados | P2 · média | O serviço gerenciado tem cotas. Não confundir o código aberto com infraestrutura ilimitada; habilitar políticas de acesso e separar chaves públicas de secretas. |
| **R50 · [DuckDB](https://github.com/duckdb/duckdb)** | Consultar tabelas locais de estatísticas e séries temporais, inclusive CSV/Parquet, sem administrar um servidor. | MIT | P2 · baixa/média | Útil para análise, não substitui sozinho autenticação e backend multiusuário. Guardar o arquivo em armazenamento persistente. |
| **R51 · [JupyterLab](https://github.com/jupyterlab/jupyterlab)** | Ambiente de investigação dos dados e registro de etapas científicas reproduzíveis. | BSD-3-Clause | P1 · baixa/média | Limpar saídas com tokens e dados excessivos antes do commit; registrar versões e parâmetros, não apenas capturas de tela. |
| **R52 · [Pixi](https://github.com/prefix-dev/pixi)** | Fixar ambientes e dependências, inclusive científicas/binárias, em Linux, macOS e Windows. | BSD-3-Clause | P2 · média | Escolher o ambiente indicado pelo notebook. Não misturar gestores e versões de GDAL sem controle. |
| **R53 · [pytest](https://github.com/pytest-dev/pytest)** | Testar cálculos, unidades, máscaras, transformação de coordenadas e estatísticas com pequenos casos conhecidos. | MIT | P1 · baixa/média | Criar testes científicos, não só teste de importação. Exemplos: diferença nula de uma imagem consigo mesma e NoData fora da área. |
| **R54 · [Playwright](https://github.com/microsoft/playwright)** | Verificar abertura da aplicação, mudança de datas, funcionamento dos controles e telas de erro. | Apache-2.0 | P2 · média | Cobrir também link público, resolução de celular e indisponibilidade de API; não é uma validação científica. |
| **R55 · [axe-core](https://github.com/dequelabs/axe-core)** | Detectar parte dos problemas de acessibilidade da interface e apoiar revisão de controles e conteúdo. | MPL-2.0 | P2 · média | Teste automático não cobre tudo: mapa precisa de legenda, alternativa textual, teclado e informação não dependente apenas de cor. |
| **R56 · [Gitleaks](https://github.com/gitleaks/gitleaks)** | Inspecionar arquivos e histórico em busca de tokens, senhas e credenciais antes da publicação. | MIT na ferramenta | P2 · baixa/média | O README informa foco futuro em correções de segurança, sem novas funcionalidades. Se um segredo vazou, revogar; apagar o arquivo não basta. |
| **R57 · [Docker Compose](https://github.com/docker/compose)** | Reproduzir API, servidor de tiles e serviços locais quando a arquitetura tiver vários componentes. | Apache-2.0 | P3 · média | Não é hospedagem nem VPS. Docker Desktop tem termos próprios; evitar complexidade desnecessária para uma demo estática. |

### 8.6. Design, documentação e apresentação

| ID / repositório | Aplicação específica | Licença principal | Prioridade / dificuldade | Limite ou cuidado |
|---|---|---|---|---|
| **R58 · [Penpot](https://github.com/penpot/penpot)** | Desenhar as telas do explorador de mudanças e testar a organização dos controles antes de implementá-los. | MPL-2.0 no código | P2 · baixa/média | Hospedagem própria tem custo operacional. Verificar as condições da modalidade de uso; protótipo visual não é resultado científico. |
| **R59 · [Excalidraw](https://github.com/excalidraw/excalidraw)** | Rascunhar a jornada do usuário e explicar a arquitetura para a equipe. | MIT | P1 · baixa | Os serviços adicionais pagos são distintos do editor aberto. Não incluir credenciais nos diagramas. |
| **R60 · [OBS Studio](https://github.com/obsproject/obs-studio)** | Gravar a demonstração e manter um vídeo de contingência caso internet ou serviços falhem. | GPL-2.0 ou posterior | P1 · baixa | Esconder tokens, notificações e dados pessoais. A gravação deve mostrar o que realmente funciona, com limitações declaradas. |
| **R61 · [Kdenlive](https://github.com/KDE/kdenlive)** | Editar o vídeo, acrescentar legendas e organizar explicações do problema, dos dados e dos resultados. | GPL-3.0; conferir avisos da distribuição | P2 · baixa/média | Repositório GitHub consultado como espelho KDE; desenvolvimento também no KDE Invent. Conferir direitos de músicas e elementos visuais. |
| **R62 · [Mermaid](https://github.com/mermaid-js/mermaid)** | Manter diagramas de fluxo em texto junto da documentação Markdown do projeto. | MIT | P2 · baixa/média | Diagramas devem refletir a arquitetura implementada. Não representam um recurso obrigatório do desafio. |

**Referências adicionais para suporte e licenciamento específicos:** [QGIS — licença](https://qgis.org/license/), [ISCE3 — LICENSE](https://github.com/isce-framework/isce3/blob/develop/LICENSE), [SNAP — leitor NISAR](https://step.esa.int/main/wp-content/help/versions/14.0.0/snap-toolboxes/eu.esa.microwavetbx.sar.io/readers/ImportNisar.html), [OpenLayers — exemplo COG](https://openlayers.org/en/latest/examples/cog.html), [Docker Desktop — termos](https://docs.docker.com/subscription/desktop-license/).

### Roteiro de leitura dos exemplos NISAR

No Cookbook, procurar exemplos de **busca com earthaccess, abertura de HDF5, conversão de pixels, GCOV, análise temporal e detecção de mudança**. Alguns notebooks podem estar em atualização ou fora do sumário publicado: verificar o arquivo atual, o ambiente recomendado e os dados esperados antes de adotá-los.

A sequência sugerida é: **entender um produto → visualizar um recorte → conferir metadados → repetir em outra data comparável → somente então comparar**. Não copiar um notebook inteiro sem saber quais premissas ele usa.

Fonte: [repositório NISAR Cookbook](https://github.com/ASFOpenSARlab/NISAR_Cookbook).

---

<a id="ia"></a>
## 9. Ferramentas de IA e seus limites

### 9.1 Apoio à equipe — sem tornar a demonstração dependente de IA

| Ferramenta / fonte | Uso proposto para a Xandar | Gratuidade e cuidado |
|---|---|---|
| [ChatGPT](https://help.openai.com/en/articles/9275245-chatgpt-free-tier-faq) | Explicar documentação, revisar código e testes, estruturar README e revisar a apresentação. | Plano gratuito com limites. Conferir afirmações científicas na fonte; não enviar tokens ou credenciais. |
| [GitHub Copilot](https://docs.github.com/pt/copilot/get-started/plans) | Apoiar implementação e depuração no editor. | Copilot Free tem cotas. Sugestão de código não dispensa testes, verificação de dependências e licenças. |
| [Google AI Studio / Gemini API](https://ai.google.dev/gemini-api/docs/pricing) | Experimentar explicações textuais sobre resultados previamente calculados ou apoio ao desenvolvimento. | Há modelos/modalidades com camada gratuita; verificar modelo, limites e política de uso dos dados. Não presumir que todas as modalidades de imagem sejam grátis. |
| [NotebookLM / Gemini Notebook](https://support.google.com/notebooklm/answer/16213268) | Estudar um conjunto fechado de manuais e tutoriais, mantendo referências às fontes. | Plano e limites próprios. A ajuda consultada redirecionou para a marca Gemini Notebook; conferir o produto disponível na conta. |
| [Ollama](https://github.com/ollama/ollama) | Alternativa local para assistência textual/código. | Sem taxa de API na execução local, mas depende do equipamento e da licença do modelo. |
| [Gemini Image / Nano Banana](https://ai.google.dev/gemini-api/docs/image-generation) | Ilustrações conceituais para divulgação e elementos explicativos, claramente identificados. | Aplicativo e API têm regras distintas. Não usar para inventar ou “melhorar” evidências geográficas; consultar [preços](https://ai.google.dev/gemini-api/docs/pricing). |

**Recomendação:** a tela de resultados deve continuar funcionando mesmo que uma API de IA esteja indisponível. Uma explicação editorial fixa e revisada pode ser suficiente para a primeira demonstração.

### 9.2 IA dentro da análise científica

Para classificar pixels ou mudanças, começar por uma linha de base interpretável e mensurável. Só testar redes neurais ou modelos fundacionais quando houver dados, rótulos, compatibilidade e tempo para comparar com essa linha de base.

O **Prithvi-EO-2.0** está associado a observações ópticas HLS. **TerraMind** explora modalidades que incluem sensores diferentes; isso não demonstra transferência pronta para NISAR. Usar um modelo treinado com outra banda de radar ou outra representação exige adaptação e avaliação, não apenas trocar o arquivo de entrada.

Fontes: [Prithvi-EO-2.0](https://github.com/NASA-IMPACT/Prithvi-EO-2.0), [TerraMind](https://github.com/IBM/terramind), [TorchGeo](https://github.com/torchgeo/torchgeo).

**Evitar:** um chat analisar apenas uma captura colorida e declarar, sem procedimento validado, “este local sofreu desmatamento” ou “esta casa foi inundada”. IA generativa pode ajudar a explicar resultados; não deve fabricar observações, classes, coordenadas ou estatísticas.

---

<a id="infraestrutura"></a>
## 10. Hospedagem, banco, armazenamento e VPS

**Vercel não é uma VPS.** É uma plataforma gerenciada de implantação. Banco de dados, armazenamento de arquivos e processamento de radar também são necessidades diferentes. Escolher apenas os serviços que a aplicação realmente exigir.

### 10.1 Opções específicas para este projeto

| Serviço / fonte primária | Uso proposto | Gratuidade / limite importante | Decisão sugerida |
|---|---|---|---|
| [Streamlit Community Cloud](https://docs.streamlit.io/deploy/streamlit-community-cloud) | Publicar a aplicação Python com resultados pequenos. | Serviço gratuito sujeito aos limites e políticas da plataforma. | Candidato ao caminho mais curto; não executar grandes processamentos em cada interação. |
| [Cloudflare Pages](https://developers.cloudflare.com/pages/platform/limits/) | Hospedar frontend estático e artefatos pequenos. | Plano gratuito; **25 MiB por arquivo individual**. Funções usam regras próprias de Workers. | Candidato forte para React/Vite com dados preparados. |
| [Vercel Hobby](https://vercel.com/docs/plans/hobby) | Publicar frontend, conforme a stack escolhida. | Gratuito para uso **pessoal e não comercial**, com cotas e regras de colaboração. | Avaliar elegibilidade do projeto e da equipe; não usar como processamento SAR pesado. |
| [Supabase Free](https://supabase.com/pricing) | Metadados, estudos salvos, AOIs, estatísticas e autenticação opcional. | **500 MB de banco**, **1 GB de arquivos**, dois projetos ativos; pausa por inatividade. | Acrescentar só quando houver gravação compartilhada. Não planejar um acervo inteiro de cenas HDF5 nesse limite. |
| [Supabase + PostGIS](https://supabase.com/docs/guides/database/extensions/postgis) | Consultas espaciais sobre polígonos e pontos. | Usa recursos do projeto/banco contratado. | Útil para cruzamentos por área; dispensável se tudo puder ser pré-calculado. |
| [Cloudflare R2](https://developers.cloudflare.com/r2/pricing/) | Guardar COGs, PMTiles e derivados maiores. | Camada gratuita Standard de **10 GB-mês**, com cotas de operações; saída de dados sem tarifa de egress. Excedentes podem gerar cobrança. | Não confundir “egress gratuito” com “tudo ilimitado”. Conferir faturamento, acesso público, CORS e operações. |
| [Render Free](https://render.com/docs/free) | Hospedar uma API Python leve ou servidor de tiles pequeno, após testar. | Suspende após 15 minutos sem tráfego; disco efêmero; Postgres gratuito expira após 30 dias. | Não guardar dados únicos no disco local nem depender de processamento pesado. Testar retomada e memória. |
| [Google Colab](https://research.google.com/colaboratory/faq.html) | Exploração e processamento eventual em notebooks. | Gratuidade e GPU sem garantia; sessões e recursos variáveis. | Laboratório, não backend permanente. Exportar resultados antes de perder a sessão. |
| [Oracle Cloud Always Free](https://www.oracle.com/cloud/free/) | VPS real para API/processamento quando a equipe souber administrar Linux. | Recursos elegíveis limitados; disponibilidade depende de capacidade; instâncias ociosas podem ser recuperadas. | Plano condicionado, não garantia de servidor gratuito no evento. Conferir arquitetura da CPU e dependências. |
| Computador da equipe | Processar recortes e manter demonstração local. | Usa equipamento e espaço disponíveis; sem garantia de desempenho nesta pesquisa. | Primeiro plano alternativo. Medir RAM, tempo e disco com uma amostra real. |

Referências complementares: [condições Always Free](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm), [cotas Supabase](https://supabase.com/pricing), [regras de arquivos no Pages](https://developers.cloudflare.com/pages/platform/limits/).

### 10.2 O que guardar em cada lugar

Esta é uma proposta de organização:

| Conteúdo | Destino recomendado |
|---|---|
| Código, documentação, arquivos pequenos de configuração | GitHub do projeto técnico |
| HDF5 original | Armazenamento local/persistente ou bucket adequado; preservar origem e identificador |
| Raster científico derivado | GeoTIFF/COG, mantendo unidades e máscaras |
| Camada visual | PNG/tiles/PMTiles ou COG preparado para o visualizador |
| Séries e estatísticas | CSV/Parquet/JSON; banco somente se necessário |
| AOIs e metadados de estudos salvos | Supabase/PostGIS, quando houver interação persistente |
| Chaves e credenciais | Gerenciador de segredos ou variáveis protegidas do servidor, nunca Git público |
| Vídeo da demonstração | Arquivo local e plataforma de apresentação apropriada |

**Não tentar abrir um HDF5 completo diretamente como imagem do mapa.** Uma cadeia de leitura e transformação separa as matrizes científicas do material de apresentação. O guia NISAR descreve a estrutura hierárquica dos arquivos e suas particularidades de leitura. [Fonte](https://nisar-docs.asf.alaska.edu/data-format/).

### 10.3 Controle de gastos e disponibilidade

Antes de cadastrar serviços com faturamento, registrar quem é o responsável, o que é gratuito, quais excedentes cobram e como suspender o recurso. **Alerta de orçamento não deve ser tratado automaticamente como bloqueio de cobrança.**

Para o mapa, medir número e tamanho das requisições. Para a API, medir memória e tempo de resposta. Para o banco, verificar pausa, backup e regras de acesso. Uma conta gratuita não é uma promessa de disponibilidade ininterrupta.

---

<a id="arquiteturas"></a>
## 11. Quatro arquiteturas possíveis

As opções abaixo são **propostas de desenho**, construídas a partir das capacidades documentadas nas seções anteriores. Não foram implementadas nem comparadas em benchmark.

### A. Caminho curto: painel científico em Python

**Componentes candidatos:** earthaccess/asf_search → h5py + NumPy + Rasterio/GDAL → recortes e estatísticas → Streamlit + Plotly + mapa Folium/leafmap compatível.

**Hospedagem candidata:** Streamlit Community Cloud. **Plano alternativo:** aplicação local + vídeo.

**Vantagem esperada:** menos integração entre linguagens e mais foco em dados.  
**Limite:** widgets de mapa e desempenho precisam de teste; não processar toda a cena durante cada clique.  
**Pode dispensar:** VPS, banco remoto, login, API própria e GPU.

Escolher se a equipe domina Python e precisa demonstrar a ideia antes de investir numa interface customizada.

### B. Caminho web visual: resultados preparados + site estático

**Componentes candidatos:** processamento Python → COGs pequenos/estatísticas → React + Vite + OpenLayers → Cloudflare Pages; arquivos maiores em armazenamento adequado.

**Alternativa:** MapLibre com imagens/tiles preparados e comparador deslizante.

**Vantagem esperada:** liberdade visual e pouca operação de backend.  
**Limite:** CORS, suporte a HTTP Range, projeção, tamanho dos recortes e desempenho no celular precisam ser verificados.  
**Pode dispensar:** API própria, login, banco e VPS.

O OpenLayers mantém um exemplo oficial de leitura de COG no navegador. Isso fundamenta a alternativa, mas não prova desempenho para nossos arquivos. [Fonte](https://openlayers.org/en/latest/examples/cog.html).

**Minha preferência para uma demonstração com poucas áreas fixas:** avaliar este caminho quando houver alguém confortável com frontend. Não é necessário habilitar qualquer área do planeta na primeira versão.

### C. Caminho dinâmico: API, tiles e estudos salvos

**Componentes candidatos:** React/Vite + MapLibre/OpenLayers → FastAPI/TiTiler → COGs em armazenamento de objetos; Supabase/PostGIS para metadados e estudos.

**Vantagem esperada:** filtros e consultas mais flexíveis.  
**Custo/complexidade:** operação de servidor, limites de memória, autenticação, políticas de acesso, cache e manutenção.  
**Condição para escolher:** existir uma funcionalidade concreta que a opção estática não resolva.

Não permitir que usuários anônimos disparem tarefas ilimitadas, forneçam URLs arbitrárias para o servidor ler ou iniciem download/processamento de arquivos gigantes.

### D. Caminho avançado: deformação ou análise especializada

**Componentes candidatos:** GUNW/GOFF/GSLC compatíveis → NISAR QA + ferramentas especializadas, como MintPy/ISCE3 → resultados pré-calculados → interface A ou B.

**Vantagem esperada:** aprofundamento científico, se o problema justificar.  
**Risco:** geometria, coerência, correções, dependências e tempo de processamento.  
**Condição para escolher:** alguém da equipe conseguir explicar e verificar o método, com dados reais já identificados.

Não selecionar esse caminho apenas por parecer mais sofisticado. As ferramentas e o fenômeno precisam corresponder.

### Decisão rápida

| Situação da equipe | Primeira opção a testar |
|---|---|
| Forte em Python, pouca experiência em frontend | A |
| Forte em React/design e poucas áreas/datas fixas | B |
| Precisa salvar estudos ou responder consultas espaciais realmente dinâmicas | C |
| Possui conhecimento específico de interferometria/deformação | D |

---

<a id="fenomenos"></a>
## 12. Ferramentas por fenômeno estudado

As combinações são hipóteses de trabalho, não promessas de detecção operacional.

| Fenômeno | Produto / ferramentas a avaliar | Dados complementares e validação | Risco principal |
|---|---|---|---|
| **Água, inundação ou dinâmica de áreas úmidas** | GCOV; NumPy, Rasterio, scikit-image; mapas antes/depois. | ANA, chuva INMET, HLS e referência independente adequada. | Água aberta e vegetação inundada podem responder de forma diferente; “pixel escuro = inundação” é simplificação insuficiente. |
| **Mudanças em floresta, desmatamento ou pós-incêndio** | GCOV em datas comparáveis; xarray, GeoPandas e gráficos temporais. | INPE/MapBiomas, óptico e contexto temporal. | Umidade, geometria e sazonalidade também afetam o sinal; mudança radiométrica não prova desmatamento. |
| **Agricultura e umidade** | GCOV; estatísticas por talhão/área e séries temporais. SME2 apenas após avaliar maturidade. | Chuva, uso do solo e informações locais documentadas. | Não converter retorno do radar em umidade absoluta ou produtividade sem modelo e validação apropriados. |
| **Geleiras e movimento de superfície** | GOFF e ferramentas específicas; mapa de vetores e incerteza. | Dados de referência e contexto glaciológico. | Interpretar offsets, geometria e qualidade; não confundir deslocamento com simples diferença de brilho. |
| **Subsidência, terremotos ou deformação** | GUNW; MintPy/ISCE3 quando justificáveis; análise de coerência. | Referência geodésica e registros independentes do evento. | Movimento na linha de visada não é automaticamente vertical; correções e convenções precisam ser declaradas. |

Bases técnicas: [produtos NISAR](https://nisar-docs.asf.alaska.edu/products-overview/), [aplicação NASA sobre água e inundação com SAR](https://science.data.nasa.gov/disasters/data-gallery/uavsar-unet-classified), [GUNW](https://nisar-docs.asf.alaska.edu/gunw/), [GOFF](https://nisar-docs.asf.alaska.edu/goff/), [MintPy](https://github.com/insarlab/MintPy). A aplicação NASA citada usa UAVSAR como referência física, **não como substituto de dados NISAR**.

**Critério recomendado de escolha:** priorizar a combinação que tenha dados comparáveis, uma evidência independente e uma pergunta que a equipe consiga responder sem exagerar o alcance do resultado.

---

<a id="validacao"></a>
## 13. Método científico, matemática e validação

### 13.1 Procedimento mínimo proposto

| Etapa | Verificação que a equipe deve realizar |
|---|---|
| Definir a pergunta | Escrever o que queremos observar, onde, em quais datas e para qual público. |
| Verificar disponibilidade | Encontrar os arquivos reais antes de prometer funcionalidades. |
| Registrar identidade | Coleção, produto, identificador, timestamp, CRID, maturidade, polarização, frequência e geometria. |
| Conferir qualidade | Avaliar máscaras, pixels inválidos, metadados e problemas conhecidos aplicáveis. |
| Compatibilizar | Alinhar grades, projeção, área comum e canais; não comparar combinações de observação incompatíveis. |
| Preservar o dado | Guardar os originais e gerar derivados, sem sobrescrever silenciosamente as entradas. |
| Fazer uma linha de base | Comparação simples, explicável e reproduzível antes de adicionar IA. |
| Confrontar | Procurar evidência independente e áreas onde não esperamos mudança. |
| Quantificar | Definir métrica, unidade, denominador e pixels/área válidos. |
| Comunicar | Mostrar o que os dados sugerem, o que não permitem concluir e o grau de validação realizado. |

Fontes para conferir requisitos do dado: [GCOV](https://nisar-docs.asf.alaska.edu/gcov/), [problemas conhecidos](https://nisar-docs.asf.alaska.edu/nisar-known-issues/), [NISAR QA](https://github.com/isce-framework/nisarqa). A tabela é uma proposta operacional da pesquisa, não um procedimento oficial de validação da missão.

### 13.2 Potência, amplitude e decibéis

Quando a variável é **potência positiva P**, a transformação correspondente é:

`P_dB = 10 × log10(P)`

Para duas potências válidas e comparáveis:

`Δ_dB = 10 × log10(P_depois / P_antes)`

Isso é uma identidade matemática útil para uma linha de base; **não é, por si só, um detector validado de inundação, desmatamento ou umidade**. A fórmula de `20 × log10` corresponde à representação em amplitude, sob a relação apropriada entre amplitude e potência. Não aplicar uma no lugar da outra, nem calcular logaritmo de zeros/NoData sem tratamento explícito.

Para GCOV, confirmar a variável e suas unidades nos metadados e na documentação. [Referência de exploração GCOV](https://asfopensarlab.github.io/NISAR_Cookbook/gcov-intro/).

### 13.3 Validação que cabe no escopo de um protótipo

Sugestões, a adaptar ao fenômeno:

- Separar áreas de calibração e de avaliação; evitar escolher o limiar olhando apenas os resultados que se deseja mostrar.
- Quando houver rótulos independentes, apresentar matriz de confusão e métricas por classe, com tamanho e origem da amostra.
- Testar cenários sem mudança e mostrar falsos positivos, não somente um exemplo visualmente favorável.
- Registrar sensibilidade a limiar/filtro e a quantidade de pixels descartados.
- Comparar datas com escala de cores consistente; não usar realces independentes que fabriquem contraste aparente.
- Mostrar “sem observação válida” como categoria própria.

Ferramentas candidatas: [scikit-learn](https://github.com/scikit-learn/scikit-learn), [pytest](https://github.com/pytest-dev/pytest), [SciPy](https://github.com/scipy/scipy), [pyproj](https://github.com/pyproj4/pyproj).

**Resultados não obtidos não devem ser preenchidos com números exemplificativos sem rótulo.** Áreas, percentuais, acurácia e latência deverão ser medidas pela equipe. Para medir área, explicar a projeção ou método geodésico; para medir velocidade da aplicação, registrar equipamento e condições do teste.

---

<a id="interface"></a>
## 14. Experiência do usuário e demonstração

### Interface mínima sugerida

A primeira tela deve responder: **que lugar é este, de quais datas são os dados, o que está sendo comparado e o que posso concluir?**

Sugestão de organização: seletor do estudo → mapa antes/depois ou alternância temporal → legenda com unidade → gráfico/estatística → painel de dados, método e limitações.

**Critérios de aceitação propostos:**

- [ ] O visitante consegue entender o estudo sem conhecer siglas SAR.
- [ ] Datas, fontes e unidades estão visíveis.
- [ ] Pixels inválidos e áreas sem cobertura não parecem “sem alteração”.
- [ ] O mapa oferece alternativa textual e controles acessíveis por teclado.
- [ ] A interface não depende apenas de vermelho/verde para transmitir a informação.
- [ ] Comparador, zoom e seleção de datas funcionam em um celular.
- [ ] Há mensagem clara para arquivo indisponível, cota excedida ou API fora do ar.
- [ ] O vídeo mostra funcionalidades reais e distingue resultados de funcionalidades planejadas.
- [ ] A apresentação diferencia observação, hipótese, classificação e validação.

Penpot, Excalidraw, axe-core, Playwright, OBS e Kdenlive estão no catálogo para apoiar esse trabalho. A lista de aceitação é uma proposta da equipe, não uma reprodução de regras oficiais de submissão.

### Mapas offline: atenção aos termos

Guardar os **nossos derivados** para uma demonstração local é diferente de baixar em massa o mapa-base de terceiros. A política dos tiles padrão do OpenStreetMap proíbe pré-carregamento e download para uso offline. Para contingência, usar camadas próprias ou um fornecedor que permita esse uso; preservar atribuição.

Fonte: [OSM Tile Usage Policy](https://operations.osmfoundation.org/policies/tiles/).

---

<a id="nao-adotar"></a>
## 15. O que não adotar automaticamente

| Opção ou prática | Por que não entra como dependência automática |
|---|---|
| **Todo tutorial Sentinel-1** | Sensor, polarização, geometria, formato e correções podem diferir. Exigir adaptação documentada para NISAR. |
| **Prithvi como detector NISAR pronto** | O repositório descreve outra base de observação; incluir apenas como complemento óptico ou pesquisa de adaptação. |
| **Modelo generativo para “completar” áreas sem observação** | Se usado, o resultado é simulado/gerado, não uma medida real do satélite. Para a primeira versão, preferir sinalizar falta de dados. |
| **Série temporal longa com maturidades misturadas** | Mudanças de processamento podem interferir na comparação; consultar os avisos do ASF. |
| **Processamento global por clique** | Aumenta custo, latência e risco; primeiro provar o fluxo numa área pequena. |
| **Login obrigatório na demo** | Pode impedir a banca de acessar; usar somente se a funcionalidade exigir e as regras permitirem. |
| **Banco para dados somente de leitura e fixos** | Arquivos estáticos podem bastar, reduzindo serviços e pontos de falha. |
| **GPU e modelo grande sem teste prévio** | Disponibilidade gratuita não é garantida; custo e desempenho não foram medidos. |
| **Tile server de demonstração de terceiros** | Não é infraestrutura garantida para nossa entrega. |
| **GitHub para guardar cenas enormes** | Repositório de código não deve virar armazenamento principal de dados volumosos. |

As justificativas combinam as limitações documentadas nas seções anteriores com recomendações de escopo desta pesquisa.

### Repositórios encontrados, mas sem reutilização liberada por esta pesquisa

**fastice/nisarhdf** — referência especializada, inclusive para fluxos ligados ao gelo. A licença não ficou claramente confirmada na página consultada. Usar inicialmente como referência e conferir os termos antes de copiar código. [Repositório](https://github.com/fastice/nisarhdf).

**cloudtostreet/Sen1Floods11** — referência de pesquisa em inundação com Sentinel-1. Não foi confirmada nesta pesquisa uma licença suficiente para todo o conjunto código + dados. Não tratar como dataset NISAR nem como autorização automática de redistribuição. [Repositório](https://github.com/cloudtostreet/Sen1Floods11).

**“Licença não confirmada” não significa necessariamente ausência de licença.** Significa que falta uma verificação antes de adotar. Essas duas referências não integram os 62 repositórios da tabela principal.

---

<a id="licencas"></a>
## 16. Licenças, segurança e rastreabilidade

### 16.1 Quatro camadas de verificação

| Camada | O que registrar |
|---|---|
| **Código** | Repositório, revisão/versão, arquivo de licença, avisos e modificações. |
| **Dados** | Missão/instrumento, coleção, identificador, versão, data, unidade, licença/termos e referência de citação. |
| **Modelo de IA** | Licença do código, licença dos pesos/checkpoint, dados de treinamento relevantes e condições de uso. |
| **Serviço** | Plano, gratuidade, cobrança por excedente, necessidade de cartão e política de tratamento dos dados. |

Um repositório público sem licença não equivale a autorização irrestrita para reutilizar seu código. Conferir permissões antes de incorporar. [Fonte](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository).

O uso de um aplicativo de edição para produzir um vídeo não deve ser confundido com incorporar o código desse aplicativo ao nosso software. Dependências, trechos copiados e componentes distribuídos merecem análise própria. Para GPL/MPL e combinações de licenças, examinar as obrigações da versão e da forma de distribuição escolhidas, sem presumir compatibilidade automática.

Quanto a imagens e identidade NASA, observar atribuição, exceções de materiais de terceiros e restrições de marcas; participação no evento não significa endosso institucional do produto. [Diretrizes NASA](https://www.nasa.gov/nasa-brand-center/images-and-media/).

### 16.2 Segurança mínima

Credenciais Earthdata, tokens de nuvem, senhas e chaves secretas devem ficar fora de commits, notebooks publicados e código entregue ao navegador. Se houver exposição, revogar e substituir.

No Supabase, **chaves publicáveis** podem ser usadas no cliente conforme o desenho de segurança; **chaves secretas / service role** não. A autorização dos dados precisa das políticas adequadas, inclusive RLS. [Fonte](https://supabase.com/docs/guides/getting-started/api-keys).

Para o protótipo, evitar coletar dados pessoais sem necessidade. Preferir acesso de leitura à demonstração. Na API de tiles, limitar origens de arquivos, tamanho e operações admitidas.

### 16.3 Arquivos de documentação sugeridos

| Arquivo | Conteúdo |
|---|---|
| `README.md` | Problema, escopo, execução, demonstração e limitações. |
| `DATA_SOURCES.md` | Coleções, identificadores, versões, datas, condições e referências. |
| `METHOD.md` | Etapas científicas, parâmetros, unidades, máscaras e método de validação. |
| `THIRD_PARTY_NOTICES.md` | Código, bibliotecas e ativos reutilizados, com avisos pertinentes. |
| `AI_USAGE.md` | Ferramentas de IA, finalidade, modelo quando conhecido e revisão humana. |
| `LICENSE` | Licença escolhida para o trabalho original, compatível com a entrega e os componentes. |
| Manifesto de dependências e lockfile | Ambiente reproduzível com versões fixadas após teste. |
| `DEMO.md` | Roteiro de apresentação, links e plano alternativo. |

São recomendações de organização, não uma afirmação de que todos esses nomes de arquivo são obrigatórios pelo evento.

---

<a id="roteiro"></a>
## 17. Roteiro de decisão e plano de contingência

### 17.1 Antes de fechar a stack

As primeiras decisões devem ser **fenômeno + local + intervalo de datas + público-alvo**. Só depois escolher os componentes que resolvem as necessidades encontradas.

- [ ] Registrar a pergunta científica em uma frase.
- [ ] Localizar ao menos duas observações comparáveis, ou outra configuração exigida pelo método.
- [ ] Conferir maturidade, versão, polarizações, geometria e qualidade.
- [ ] Definir quem validará a interpretação do radar.
- [ ] Abrir uma amostra e medir tempo, memória e tamanho dos derivados.
- [ ] Escolher somente um caminho principal de interface.
- [ ] Justificar individualmente banco, login, API, VPS e GPU; dispensar o que não resolver uma necessidade.
- [ ] Confirmar licença e reprodução do exemplo usado como ponto de partida.
- [ ] Verificar plano gratuito, cobrança, armazenamento e acesso dos avaliadores.
- [ ] Reconferir enunciado e recursos oficiais do desafio antes da entrega.

**Esta pesquisa não certifica o que pode ser implementado antes do evento.** Realizar o desenvolvimento da solução apenas no período e nas condições autorizados pela organização; separar estudo/preparação de implementação submetida.

### 17.2 Teste de seleção de repositório

Não escolher apenas por número de estrelas. Para cada candidato, verificar: resolve a nossa necessidade; é compatível com o produto; tem licença clara; possui exemplo compreensível; usa ambiente reproduzível; funciona com uma amostra; tem custo viável; tem plano alternativo.

Registrar o resultado como **“documentado”**, **“testado pela equipe”** ou **“adotado”**. No momento desta publicação, a pesquisa está no primeiro nível.

### 17.3 Contingências

| Falha | Alternativa preparada pela equipe |
|---|---|
| Serviço de dados indisponível | Amostra baixada legalmente, com origem, identificadores e datas preservados. |
| Harmony não executa a operação necessária | Recorte/conversão local com ferramentas compatíveis. |
| Backend suspenso ou com memória insuficiente | Mapa de derivados pré-calculados; não depender de recomputação ao vivo. |
| Cota/indisponibilidade da IA | Texto revisado, cálculos determinísticos e aplicação sem chat. |
| GPU indisponível | Linha de base pequena e explicável em CPU, previamente medida. |
| Internet do local falha | Demonstração local com camadas autorizadas e vídeo real gravado. |
| Banco pausado | Exportação dos dados de demonstração e modo de leitura estático. |
| Produto não existe para a área/data | Outra área/data documentada; não inventar observações para manter a narrativa. |

### 17.4 Ficha de decisão da Xandar

| Campo | Preencher pela equipe |
|---|---|
| Fenômeno e pergunta | |
| Área e intervalo de datas | |
| Público que usará o resultado | |
| Produto NISAR, coleção e maturidade | |
| Arquivos reais selecionados | |
| Fonte independente de validação | |
| Caminho de arquitetura: A, B, C ou D | |
| Ferramentas essenciais | |
| Recursos descartados e motivo | |
| Custo previsto, cotas e responsável | |
| Teste executado e evidência | |
| Limitação científica mais importante | |
| Plano de contingência | |
| Responsáveis e aprovação da equipe | |

---

<a id="contribuir"></a>
## 18. Como contribuir para este catálogo

O material permanece aberto a sugestões e revisões. Qualquer integrante pode propor uma ferramenta, corrigir uma condição de uso ou registrar que um exemplo foi testado.

**Modelo de contribuição:**

> **Ferramenta/repositório:**  
> **Link oficial:**  
> **Que necessidade específica do Dancing with the SARs resolve:**  
> **Produto/formato NISAR suportado ou adaptação necessária:**  
> **Licença do código, dados/modelos e custo do serviço:**  
> **Teste realizado, versão e resultado:**  
> **Limites e alternativa:**  
> **Data e responsável pela avaliação:**

Manter o catálogo de preparação separado dos compromissos da implementação. Uma ferramenta listada **não foi automaticamente escolhida** pela equipe.

**Mensagem de commit sugerida para adicionar este documento:**

`Adiciona catálogo específico de tecnologias e repositórios para Dancing with the SARs`

---

<a id="referencias"></a>
## 19. Referências técnicas prioritárias

Todos os links do catálogo são referências para os respectivos recursos. A relação abaixo facilita a rechecagem dos pontos que mais afetam as decisões; **consulta em 10/11/2026**.

| Tema | Fonte primária / referência |
|---|---|
| Enunciado global, a reconferir por erro de acesso | [Space Apps — Dancing with the SARs](https://www.spaceappschallenge.org/2026/challenges/dancing-with-the-sars/) |
| Reprodução do desafio pelo organizador | [HackAthlone / TUS](https://hackathlone.com/challenges/dancing-with-the-sars) |
| Documentação central | [NISAR Data User Guide — ASF](https://nisar-docs.asf.alaska.edu/) |
| Maturidade e calendário do acervo | [Available Data](https://nisar-docs.asf.alaska.edu/availability-overview/) |
| Problemas conhecidos | [Known Issues](https://nisar-docs.asf.alaska.edu/nisar-known-issues/) |
| Produto para análise de retroespalhamento | [GCOV](https://nisar-docs.asf.alaska.edu/gcov/) |
| Fase interferométrica | [GUNW](https://nisar-docs.asf.alaska.edu/gunw/) |
| Deslocamentos por pixels | [GOFF](https://nisar-docs.asf.alaska.edu/goff/) |
| Estrutura dos arquivos | [Data Format](https://nisar-docs.asf.alaska.edu/data-format/) |
| Ferramentas abertas com exemplos | [Using Open Source](https://nisar-docs.asf.alaska.edu/using-open-source/) |
| Tutoriais | [NISAR Cookbook](https://asfopensarlab.github.io/NISAR_Cookbook/) |
| Transformações de dados | [Harmony](https://nisar-docs.asf.alaska.edu/harmony/) |
| Funcionalidades prontas versus planejadas | [ASF Roadmap](https://nisar-docs.asf.alaska.edu/roadmap/) |
| Leitor NISAR do SNAP | [Documentação SNAP 14](https://step.esa.int/main/wp-content/help/versions/14.0.0/snap-toolboxes/eu.esa.microwavetbx.sar.io/readers/ImportNisar.html) |
| COG diretamente na web | [OpenLayers — exemplo](https://openlayers.org/en/latest/examples/cog.html) |
| Dados ópticos auxiliares | [NASA HLS](https://hls.gsfc.nasa.gov/data-access-and-tools/) |
| Contexto físico de inundação e radar | [NASA — UAVSAR, exemplo de pesquisa](https://science.data.nasa.gov/disasters/data-gallery/uavsar-unet-classified) |
| Termos de mapa-base | [OSM — uso de tiles](https://operations.osmfoundation.org/policies/tiles/) |
| Banco gratuito | [Supabase Pricing](https://supabase.com/pricing) |
| Frontend estático | [Cloudflare Pages — limites](https://developers.cloudflare.com/pages/platform/limits/) |
| Armazenamento de objetos | [Cloudflare R2 — preços](https://developers.cloudflare.com/r2/pricing/) |
| Hospedagem gerenciada | [Vercel Hobby](https://vercel.com/docs/plans/hobby) |
| API gratuita e suas restrições | [Render Free](https://render.com/docs/free) |
| VPS condicionada a disponibilidade | [Oracle Cloud Free](https://www.oracle.com/cloud/free/) |
| Limites de computação em notebooks | [Colab FAQ](https://research.google.com/colaboratory/faq.html) |
| Direitos de reutilização | [GitHub — licenciar um repositório](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository) |

**Síntese:** nossa prioridade deve ser demonstrar uma mudança real, com origem rastreável e interpretação honesta, em uma interface que funcione. Este catálogo amplia as escolhas; a decisão final pertence à equipe.
