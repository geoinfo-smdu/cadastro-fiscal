# Georeferenciamento da base de dados de IPTU do Município da Cidade de São Paulo

Está disponível para download através do site do [Geosampa](http://geosampa.prefeitura.sp.gov.br/PaginasPublicas/_SBC.aspx) os arquivos de IPTU desde 1995 até agora. Esses arquivos contem registros de todos os lancamentos de IPTU além de informações que podem ser bastante úteis para compreender a cidade, seus parcelamentos e edificações.

## Objetivo

Com a finalidade de facilitar o acesso e estimular de maneira pedagógica a análise dos dados abertos disponíveis criamos aqui um passo-a-passo de como carregar as informações do IPTU, fazer algumas análises básicas e posteriormente georeferenciar essas informações.

## Metodologia

Para isso vamos utilizar a linguagem de programação Python e utilizar a biblioteca de analise de dados Pandas e GeoPandas, e exemplificar passo-a-passo como acessar, analisar e georeferenciar os dados do IPTU

## Download da Tabela de IPTU

A [Tabela de IPTU de 2019 está disponível nesse link](mapas.geosampa.prodam/PaginasPublicas/downloadIfr.aspx?orig=DownloadCamadas&arq=11_Cadastro%5C%5CIPTU%5C%5CXLS_CSV%5C%5CIPTU2019&arqTipo=XLS_CSV), é disponibilizada em formato CSV e é disponibilizada em um arquivo ZIP.

Vamos expandir o arquivo ZIP na pasta Downloads e carregar o arquivo na memória com o Pandas


```python
%%time

import pandas as pd

df = pd.read_csv("./downloads/IPTU2019.CSV", error_bad_lines=False, encoding='iso-8859-9', sep=';')
df.head()
```

    Wall time: 34.9 s
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>NUMERO DO CONTRIBUINTE</th>
      <th>ANO DO EXERCICIO</th>
      <th>NUMERO DA NL</th>
      <th>DATA DO CADASTRAMENTO</th>
      <th>TIPO DE CONTRIBUINTE 1</th>
      <th>CPF/CNPJ DO CONTRIBUINTE 1</th>
      <th>NOME DO CONTRIBUINTE 1</th>
      <th>TIPO DE CONTRIBUINTE 2</th>
      <th>CPF/CNPJ DO CONTRIBUINTE 2</th>
      <th>NOME DO CONTRIBUINTE 2</th>
      <th>...</th>
      <th>ANO DA CONSTRUCAO CORRIGIDO</th>
      <th>QUANTIDADE DE PAVIMENTOS</th>
      <th>TESTADA PARA CALCULO</th>
      <th>TIPO DE USO DO IMOVEL</th>
      <th>TIPO DE PADRAO DA CONSTRUCAO</th>
      <th>TIPO DE TERRENO</th>
      <th>FATOR DE OBSOLESCENCIA</th>
      <th>ANO DE INICIO DA VIDA DO CONTRIBUINTE</th>
      <th>MES DE INICIO DA VIDA DO CONTRIBUINTE</th>
      <th>FASE DO CONTRIBUINTE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0010030001-4</td>
      <td>2019</td>
      <td>1</td>
      <td>12/01/19</td>
      <td>PESSOA FISICA (CPF)</td>
      <td>XXXXXX0214XXXX</td>
      <td>MARCIO MOURCHED</td>
      <td>NaN</td>
      <td></td>
      <td>NaN</td>
      <td>...</td>
      <td>1924</td>
      <td>1</td>
      <td>13,00</td>
      <td>Loja</td>
      <td>Comercial horizontal - padrão B</td>
      <td>De esquina</td>
      <td>0,20</td>
      <td>1963</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0010030002-2</td>
      <td>2019</td>
      <td>1</td>
      <td>12/01/19</td>
      <td>PESSOA FISICA (CPF)</td>
      <td>XXXXXX0214XXXX</td>
      <td>MARCIO MOURCHED</td>
      <td>NaN</td>
      <td></td>
      <td>NaN</td>
      <td>...</td>
      <td>1944</td>
      <td>1</td>
      <td>6,00</td>
      <td>Loja</td>
      <td>Comercial horizontal - padrão B</td>
      <td>Normal</td>
      <td>0,20</td>
      <td>1963</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0010030003-0</td>
      <td>2019</td>
      <td>1</td>
      <td>12/01/19</td>
      <td>PESSOA FISICA (CPF)</td>
      <td>XXXXXX0214XXXX</td>
      <td>MARCIO MOURCHED</td>
      <td>NaN</td>
      <td></td>
      <td>NaN</td>
      <td>...</td>
      <td>1965</td>
      <td>2</td>
      <td>7,85</td>
      <td>Loja</td>
      <td>Comercial horizontal - padrão B</td>
      <td>Normal</td>
      <td>0,32</td>
      <td>1963</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0010030004-9</td>
      <td>2019</td>
      <td>1</td>
      <td>12/01/19</td>
      <td>PESSOA FISICA (CPF)</td>
      <td>XXXXXX2094XXXX</td>
      <td>AUGUSTO CESAR DE MATTOS JUNIOR</td>
      <td>NaN</td>
      <td></td>
      <td>NaN</td>
      <td>...</td>
      <td>1944</td>
      <td>1</td>
      <td>6,05</td>
      <td>Loja</td>
      <td>Comercial horizontal - padrão B</td>
      <td>Normal</td>
      <td>0,20</td>
      <td>1963</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0010030005-7</td>
      <td>2019</td>
      <td>1</td>
      <td>12/01/19</td>
      <td>PESSOA FISICA (CPF)</td>
      <td>XXXXXX2094XXXX</td>
      <td>AUGUSTO CESAR DE MATTOS JUNIOR</td>
      <td>NaN</td>
      <td></td>
      <td>NaN</td>
      <td>...</td>
      <td>1944</td>
      <td>1</td>
      <td>6,70</td>
      <td>Loja</td>
      <td>Comercial horizontal - padrão B</td>
      <td>Normal</td>
      <td>0,20</td>
      <td>1963</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 35 columns</p>
</div>



Temos 35 colunas na base do IPTU conforme a lista a seguir:


```python
list(df.columns)
```




    ['NUMERO DO CONTRIBUINTE',
     'ANO DO EXERCICIO',
     'NUMERO DA NL',
     'DATA DO CADASTRAMENTO',
     'TIPO DE CONTRIBUINTE 1',
     'CPF/CNPJ DO CONTRIBUINTE 1',
     'NOME DO CONTRIBUINTE 1',
     'TIPO DE CONTRIBUINTE 2',
     'CPF/CNPJ DO CONTRIBUINTE 2',
     'NOME DO CONTRIBUINTE 2',
     'NUMERO DO CONDOMINIO',
     'CODLOG DO IMOVEL',
     'NOME DE LOGRADOURO DO IMOVEL',
     'NUMERO DO IMOVEL',
     'COMPLEMENTO DO IMOVEL',
     'BAIRRO DO IMOVEL',
     'REFERENCIA DO IMOVEL',
     'CEP DO IMOVEL',
     'QUANTIDADE DE ESQUINAS/FRENTES',
     'FRACAO IDEAL',
     'AREA DO TERRENO',
     'AREA CONSTRUIDA',
     'AREA OCUPADA',
     'VALOR DO M2 DO TERRENO',
     'VALOR DO M2 DE CONSTRUCAO',
     'ANO DA CONSTRUCAO CORRIGIDO',
     'QUANTIDADE DE PAVIMENTOS',
     'TESTADA PARA CALCULO',
     'TIPO DE USO DO IMOVEL',
     'TIPO DE PADRAO DA CONSTRUCAO',
     'TIPO DE TERRENO',
     'FATOR DE OBSOLESCENCIA',
     'ANO DE INICIO DA VIDA DO CONTRIBUINTE',
     'MES DE INICIO DA VIDA DO CONTRIBUINTE',
     'FASE DO CONTRIBUINTE']



Isso abre uma série de possibilidades de análises, sejam elas individuais ou combinadas, como por exemplo pesquisar a quantidade de construções da década de 1970


```python
%%time
df_decada_70 = df[df['ANO DA CONSTRUCAO CORRIGIDO'].between(1970, 1980)].index

print(len(df_decada_70)) # Total de unidades/lancamentos construidas na década de 1970
print(len(df)) # Total de unidades/lancamentos total

print(len(df_decada_70)/len(df)) # Percentual de unidades/lancamentos produzidos na década de 1970
```

    828888
    3447112
    0.24045867961354317
    Wall time: 359 ms
    

Na máquina que está realiando esses testes a performance não está restringindo as análises. O arquivo demora cerca de 34 segundos para carregar e a análise acima cerca de 359ms. Vamos fazer algumas somas agora.


```python

```
