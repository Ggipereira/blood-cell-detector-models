DCCS-IA: Pipeline de Análise Hematológica com Deep Learning
Este repositório contém o desenvolvimento de um sistema inteligente para a análise de imagens de microscopia de sangue periférico. O projeto aborda as três principais frentes da visão computacional aplicadas à hematologia: Deteção, Segmentação e Classificação.

🚀 Demonstração em Tempo Real
A aplicação final, que integra os modelos desenvolvidos, está disponível no Streamlit:
[Blood Cell Detector App](https://blood-cell-detector.streamlit.app/)

📌 Visão Geral do Projeto
O objetivo principal é a automação da contagem e identificação de elementos figurados do sangue (RBC, WBC e Plaquetas), utilizando arquiteturas de última geração para garantir precisão clínica e eficiência computacional.

Componentes Principais:
Segmentação (U-Net + SAM 2.0): Isolamento de células ao nível do pixel. Utilizamos o SAM 2.0 (Segment Anything Model) para gerar máscaras de alta qualidade (Ground Truth) a partir de bounding boxes, treinando depois uma U-Net para inferência rápida.

Deteção (YOLOv8/v11): Localização e contagem de células em tempo real usando o ecossistema Ultralytics.

Classificação (EfficientNet vs. CNN Custom): Identificação de subtipos de Glóbulos Brancos (WBC).

EfficientNet-B0: Utilização de Transfer Learning e Compound Scaling.

CNN "Do Zero": Uma arquitetura personalizada desenvolvida para fins didáticos e controlo total do pipeline (baseada no guia Criar CNN do Zero).

📂 Pesos dos Modelos
Devido ao tamanho dos ficheiros, os pesos treinados (.pt, .keras, .h5) de todos os modelos estão alojados no Hugging Face:
[Modelos no Hugging Face](https://huggingface.co/Gabriel-26)

💻 Como Usar os Notebooks
Os notebooks estão preparados para ser executados preferencialmente no Google Colab, dada a necessidade de aceleração por GPU.

Configuração Inicial:

Monta o teu Google Drive para aceder aos datasets.

Instala as dependências necessárias (ex: pip install ultralytics opencv-python tensorflow).

Ordem de Execução Sugerida:

Segmentacao_SAM_UNet.ipynb: Geração de máscaras e treino da U-Net.

Treino_YOLO.ipynb: Pipeline de deteção e validação externa.

Classificacao_WBC.ipynb: Comparação entre a EfficientNet e a CNN personalizada.

Criar CNN do Zero: Se estás interessado na lógica de construção de camadas, consulta o notebook específico de classificação, que serve como um guia prático para entender convoluções, pooling e dropout.

🛠️ Tecnologias Utilizadas
Linguagem: Python

Frameworks: PyTorch, TensorFlow/Keras

Visão Computacional: OpenCV, Ultralytics, SAM 2.0

Deploy: Streamlit

✍️ Autor
Gabriel Afonso - GitHub
