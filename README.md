# Dio_Bootcamp_AI_102
Desafio de Projeto Tradução de Artigos Técnicos

O Azure AI disponibiliza dois serviços principais para tradução:
- API REST de Tradução Multilíngue
- Detecção de Idioma: Identifica automaticamente o idioma do texto.
- Tradução de Uma para Muitos: Traduza um texto para múltiplos idiomas simultaneamente.
- Transliteração de Script: Converte entre sistemas de escrita, inglês para italiano, mandarim, entre outros.
Como Funciona?
 - Detecção: A API analisa o idioma original.
 - Tradução: O texto é traduzido para os idiomas escolhidos.
 - Transliteração: Caso necessário, o texto pode ser convertido para outro alfabeto.
 - Tradução Personalizada

Criando um Modelo Personalizado
- Configure seu modelo no Portal do Tradutor Personalizado.
- Vincule um espaço de trabalho ao recurso de tradutor no Azure.
- Carregue dados de treinamento para treinar o modelo.
- Após o treinamento, chame o modelo personalizado pela API usando o parâmetro category.

Exemplos Práticos com Python
Os códigos abaixo mostram como implementar traduções automáticas usando a API do Azure Translator. Eles incluem a tradução de documentos e páginas da web.

1. Tradução de Documentos
Este código lê um arquivo .docx, traduz seu conteúdo e salva um novo documento no idioma de destino.
from docx import Document
import requests
import os

# Configuração da API
subscription_key = "SUA_CHAVE_API"
endpoint = 'https://api.cognitive.microsofttranslator.com'
location = "eastus"
language_destination = 'pt-br'
def translator_text(text, target_language):

  # Função de tradução
  headers = {'Ocp-Apim-Subscription-Key': subscription_key, 'Content-type': 'application/json'}
  params = {'api-version': "3.0", 'from': 'en', 'to': target_language}
  body = [{'text': text}]
  response = requests.post(endpoint + "/translate", params=params, headers=headers, json=body)
return response.json()[0]["translations"][0]["text"]
def translate_document(path):

  # Função para traduzir um documento
  document = Document(path)
  translated_doc = Document()
  for paragraph in document.paragraphs:
    translated_text = translator_text(paragraph.text, language_destination)
    translated_doc.add_paragraph(translated_text)
  translated_doc.save(path.replace(".docx", "_translated.docx"))

2. Tradução de Páginas da Web
O código abaixo acessa uma página web, extrai o texto, traduz e salva o resultado em um arquivo .docx.
from bs4 import BeautifulSoup
from docx import Document
import requests
def fetch_and_translate(url):

  # Baixar e traduzir conteúdo de uma página web
  response = requests.get(url)
  soup = BeautifulSoup(response.text, 'html.parser')
  text = "\n".join([p.get_text() for p in soup.find_all('p')])
  translated_text = translator_text(text, language_destination)
  doc = Document()
  doc.add_paragraph(translated_text)
  doc.save("translated_webpage.docx")
