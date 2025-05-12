# 📄 Chat com PDF usando a API do Gemini

Este projeto permite que você **interaja com o conteúdo de arquivos PDF** por meio de uma interface web feita com **Streamlit**, utilizando a **API do Gemini** para geração de respostas baseadas no conteúdo dos documentos.

---

## 🔧 1. Preparando o Ambiente

### ✅ Criar e ativar o ambiente virtual

```bash
# Criar ambiente virtual
python -m venv venv

# Ativar ambiente virtual
# Windows:
venv\Scripts\activate

# Mac/Linux:
source venv/bin/activate
```

### 📦 Criar o arquivo de dependências (`requirements.txt`)

```txt
google-generativeai
pypdf
streamlit
python-dotenv
```

### 🔁 Instalar as dependências

```bash
pip install -r requirements.txt
```

---

## 📄 2. Extração de Texto do PDF

`src/extract.py`

```python
import pypdf

def extract_text_from_pdf(pdf_file):
    reader = pypdf.PdfReader(pdf_file)
    text = "\n".join([page.extract_text() for page in reader.pages if page.extract_text()])
    return text
```

---

## 🤖 3. Conexão com a API do Gemini

`src/gemini_api.py`

```python
import google.generativeai as genai
import os
from dotenv import load_dotenv

load_dotenv()

api_key = os.getenv("GEMINI_API_KEY")
if not api_key:
    raise ValueError("Erro: variável GEMINI_API_KEY não encontrada. Verifique o .env.")

genai.configure(api_key=api_key)

def ask_gemini(question, context):
    model = genai.GenerativeModel("gemini-pro")
    response = model.generate_content(f"Baseado neste documento:\n\n{context}\n\nResponda: {question}")
    return response.text
```

---

## 💬 4. Interface com Streamlit

`src/main.py`

```python
import sys
import os
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), "..")))

import streamlit as st
from src.extract import extract_text_from_pdf
from src.gemini_api import ask_gemini

st.title("Chat com PDF usando Gemini")

uploaded_file = st.file_uploader("Faça upload de um PDF", type=["pdf"])

if uploaded_file is not None:
    text = extract_text_from_pdf(uploaded_file)
    st.session_state["context"] = text

question = st.text_input("Digite sua pergunta:")

if question and "context" in st.session_state:
    response = ask_gemini(question, st.session_state["context"])
    st.write("### Resposta:")
    st.write(response)
```

---

## 🗂️ 5. Estrutura do Projeto

```
gemini-pdf/
├── venv/                  # Ambiente virtual
├── data/                  # PDFs (opcional)
├── src/                   # Código-fonte
│   ├── __init__.py
│   ├── main.py            # Interface Streamlit
│   ├── extract.py         # Extração do PDF
│   └── gemini_api.py      # Comunicação com Gemini
├── .env                   # Chave da API Gemini
├── .gitignore
├── requirements.txt
└── README.md
```

---

## ▶️ 6. Executando o Projeto

```bash
# Ativar ambiente virtual
# Windows:
venv\Scripts\activate
# Mac/Linux:
source venv/bin/activate

# Instalar dependências
pip install -r requirements.txt

# Criar arquivo .env com a variável da chave:
# .env
GEMINI_API_KEY=SUA_CHAVE_AQUI

# Rodar o Streamlit
streamlit run src/main.py
```

---

## 🌐 7. Git: Clonando ou Subindo Projeto

### Clonando um repositório existente:

```bash
git clone SEU_REPOSITORIO_URL
cd gemini-pdf
```

### Subindo para um novo repositório:

```bash
git init
git add .
git commit -m "Primeiro commit"
git remote add origin https://github.com/seuusuario/nomedorepo.git
git push -u origin main -f
```

---

## ✅ Pronto!

Agora você pode carregar qualquer PDF e fazer perguntas diretamente sobre o conteúdo!  
A API Gemini responderá com base no documento que você enviou. 🚀
