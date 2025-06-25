# HealthStatELISA

Este projeto realiza análise estatística básica de dados relacionados à COVID-19, incluindo informações sobre vacinação, temperatura corporal, grupo sanguíneo e histórico de doenças. A fonte dos dados é uma pesquisa conduzida pela **Lviv Polytechnic National University**, apoiada pela **Central European Initiative** (projeto Stop COVID'19 – Grant 305.2825-20).

## 🧰 Tecnologias Utilizadas

* **Python 3**
* [Pandas](https://pandas.pydata.org)
* [Seaborn](https://seaborn.pydata.org)
* [Matplotlib](https://matplotlib.org)
* [OpenPyXL](https://openpyxl.readthedocs.io)

---

## ⚙️ Funcionalidades

Este projeto possui:

* Download automatizado de dados em formato `.xlsx`
* Pré-processamento robusto de dados reais
* Transformação e categorização de colunas
* Cálculo de estatísticas descritivas
* Análises por agrupamento e tabelas dinâmicas
* Visualização de dados com gráficos estatísticos

---

## 📥 Instalação e Setup

Clone o repositório e instale os requisitos:

```bash
git clone https://github.com/seu-usuario/healthcare-statistics-analysis.git
cd healthcare-statistics-analysis

# Instalar dependências (se necessário):
pip install pandas seaborn matplotlib openpyxl
```

---

## 📈 Etapas do Processamento

### 1. Download dos Dados

```python
df = pd.read_excel(
    'https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/data-science-in-health-care-basic-statistical-analysis/COVID_19.xlsx',
    'Sheet1'
)
```

### 2. Conversão da Coluna de Data/Hora

```python
def parse(x):
    y = x.split()
    t = y[1][:8]
    z = y[0] + " " + t
    return datetime.strptime(z, '%Y-%m-%d %H:%M:%S')

df = pd.read_excel(
    url,
    'Sheet1',
    na_values="NaN",
    parse_dates=['Date time'],
    index_col=0,
    date_parser=parse
)
```

---

### 3. Pré-processamento dos Dados

* Remoção de entradas com gênero indefinido:

```python
df = df.dropna(subset=['Gender'])
```

* Conversão de campos "Yes"/"No" para `True`/`False`:

```python
df['Do you vaccinated influenza?'] = df['Do you vaccinated influenza?'].map({'Yes': True, 'No': False})
```

* Conversão de tipo da coluna `Age` para categórico:

```python
df['Age'] = df['Age'].astype('category')
```

* Limpeza de campos com valores entre parênteses:

```python
for c in df.columns[1:-1]:
    df[c] = df[c].apply(lambda x: str(x) if '(' not in str(x) else str(x)[:str(x).find('(')]).astype('category')
```

---

### 4. Análise Estatística

* Informações básicas:

```python
df.describe()
df.describe(include=['category'])
```

* Contagem e frequência:

```python
df['Age'].value_counts()
df['Age'].value_counts(normalize=True)
```

* Agrupamento e estatísticas por grupo:

```python
df.groupby(['Gender'])['Maximum body temperature'].describe()
```

* Tabelas dinâmicas:

```python
pd.pivot_table(df, values='Maximum body temperature', index='Age', columns='Gender', aggfunc='mean', margins=True)
```

---

### 5. Visualização de Dados

```python
import matplotlib.pyplot as plt
import seaborn as sns
```

* Distribuição por idade e gênero:

```python
sns.countplot(x='Age', hue='Gender', data=df)
```

* Evolução temporal:

```python
df.resample('1D').sum().plot()
```

* Distribuição de temperatura entre infectados ou não:

```python
_, axes = plt.subplots(1, 2, sharey=True, figsize=(16,6))
sns.histplot(df[df['Have you had Covid`19 this year?'] == 'Yes']['Maximum body temperature'].dropna(), ax=axes[0])
sns.histplot(df[df['Have you had Covid`19 this year?'] == 'Maybe']['Maximum body temperature'].dropna(), ax=axes[1])
```

* Boxplot e Violinplot:

```python
_, axes = plt.subplots(1, 2, sharey=True, figsize=(16,6))
sns.boxplot(y='Age', x='Maximum body temperature', data=df, ax=axes[0])
sns.violinplot(y='Age', x='Maximum body temperature', data=df, ax=axes[1])
```

---

## 📌 Conclusões

* A faixa etária pode influenciar o padrão de resposta imune (temperatura corporal máxima). Jovens parecem ter uma resposta mais variável.
* Os exames ELISA foram aplicados em um grupo com possível surto de febre, visto o pico em temperaturas elevadas (38°C).
* Há correlação temporal entre aumento de temperatura corporal e a coleta de dados de grupo sanguíneo. Pode indicar picos epidêmicos ou surtos localizados..
* Os picos temporais apontam para períodos críticos de infecção — relevante para vigilância epidemiológica.

---

## 📝 Licença

Este projeto segue a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

---

## 🙋‍♂️ Contribuições

Contribuições são bem-vindas! Se você deseja corrigir algo ou adicionar novas funcionalidades, sinta-se à vontade para abrir uma *issue* ou enviar um *pull request*.
