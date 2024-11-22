# ProJ_Anti_fraude

Projeto para verificar a autenticidade de documentos
Etapas do Projeto:


Abaixo está uma estrutura de código para um projeto que utiliza o Azure AI para análise automatizada de documentos. O foco é identificar padrões de fraude, validar autenticidade e garantir a segurança no processamento de documentos sensíveis. Esse projeto inclui o uso do Azure Form Recognizer para análise de documentos e integração com algoritmos personalizados de detecção de fraude.

Etapas do Projeto:
1. Configuração do Ambiente Azure:

2. Criar os recursos necessários no Azure: Form Recognizer, Cognitive Services, etc.
Obter as chaves de API e endpoints.
Carregamento de Documentos:

3. Receber documentos via upload ou API.
Análise com Form Recognizer:

4. Extrair texto, tabelas e campos específicos dos documentos.
Validação de Autenticidade:

5. Comparar padrões de documentos com um modelo pré-treinado.
Verificar assinaturas digitais e metadados.
Detecção de Fraude:

6. Integrar algoritmos de Machine Learning (usando Azure ML) para identificar anomalias e padrões fraudulentos.
Armazenamento e Logs:

7. Armazenar os resultados no Azure Blob Storage ou SQL Database.
Registrar logs para auditoria e segurança.

Codigo utilizado para testes:

import os
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential
import logging

# Configuração do Azure Form Recognizer
AZURE_FORM_RECOGNIZER_ENDPOINT = "https://<seu-endpoint>.cognitiveservices.azure.com/"
AZURE_FORM_RECOGNIZER_KEY = "<sua-chave>"

# Configuração do logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

def analyze_document(file_path):
    """
    Analisa um documento usando o Azure Form Recognizer.
    """
    # Cliente para análise de documentos
    document_analysis_client = DocumentAnalysisClient(
        endpoint=AZURE_FORM_RECOGNIZER_ENDPOINT,
        credential=AzureKeyCredential(AZURE_FORM_RECOGNIZER_KEY)
    )

    with open(file_path, "rb") as f:
        poller = document_analysis_client.begin_analyze_document("prebuilt-document", document=f)
        result = poller.result()

    logging.info("Análise concluída.")
    return result

def validate_authenticity(result):
    """
    Valida a autenticidade do documento com base nos padrões extraídos.
    """
    # Lógica personalizada para validar autenticidade
    if "assinatura" in result.content and "válida" in result.content.lower():
        logging.info("Documento validado com sucesso.")
        return True
    else:
        logging.warning("Falha na validação da autenticidade.")
        return False

def detect_fraud(result):
    """
    Detecta possíveis padrões de fraude nos dados extraídos.
    """
    # Exemplo de detecção de fraude: verificar campos suspeitos
    for field in result.fields.values():
        if field.value and "suspeito" in field.value.lower():
            logging.warning("Padrão de fraude identificado.")
            return True

    logging.info("Nenhum padrão de fraude identificado.")
    return False

def main():
    # Caminho do documento para análise
    document_path = "documento_sensivel.pdf"

    logging.info("Iniciando análise do documento...")
    result = analyze_document(document_path)

    # Validar autenticidade
    is_valid = validate_authenticity(result)

    # Detectar fraude
    is_fraudulent = detect_fraud(result)

    # Resultado final
    if is_valid and not is_fraudulent:
        logging.info("Documento aprovado para processamento.")
    else:
        logging.error("Documento não aprovado. Ação necessária!")

if __name__ == "__main__":
    main()
