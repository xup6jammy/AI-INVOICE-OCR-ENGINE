# AI-INVOICE-OCR-ENGINE

[中文版](README_CN.md) | **English**

A complete AI-powered document OCR (Optical Character Recognition) solution based on **PaddleOCR v5**, optimized for invoice and accounting document recognition with support for Chinese and English text.

## Demo

### Chinese Invoice / 中文發票

| Original | OCR Result | Text Output |
|----------|------------|-------------|
| ![Input](demo/invoice_ch.png) | ![Output](demo/result_invoice_cn.jpg) | ![Text](demo/invoice_cn_txt.png) |

### English Invoice / 英文發票

| Original | OCR Result | Text Output |
|----------|------------|-------------|
| ![Input](demo/invoice_en.png) | ![Output](demo/result_invoice_en.jpg) | ![Text](demo/invoice_en_txt.png) |

## Architecture Diagrams

### 1. OCR Inference Pipeline
![OCR Pipeline](demo/1.png)

### 2. Model Architecture
![Model Architecture](demo/2.png)

### 3. Training Pipeline
![Training Pipeline](demo/3.png)

### 4. System Architecture
![System Architecture](demo/4.png)

### 5. Data Flow
![Data Flow](demo/5.png)

### 6. Deployment Architecture
![Deployment](demo/6.png)

### 7. Database ER Diagram
![Database ER](demo/7.png)

## Features

- **High Accuracy**: Utilizes PP-OCRv5 server models for superior recognition quality
- **Multi-language Support**: Chinese, English, and mixed text recognition
- **Document Preprocessing**: Automatic orientation correction and document unwarping
- **Bounding Box Visualization**: Draw green boxes around detected text regions
- **Text Export**: Save recognition results to TXT file
- **Easy Integration**: Simple Python API for quick deployment

## Architecture Overview

```
Input Image → Preprocessing → Text Detection → Text Recognition → Output
                                    ↓                  ↓
                              Green Boxes         TXT File
```

### Pipeline Components

| Component | Model | Description |
|-----------|-------|-------------|
| Document Orientation | PP-LCNet | Classifies document rotation (0°/90°/180°/270°) |
| Document Unwarping | UVDoc | Corrects curved/warped documents |
| Text Detection | PP-OCRv5_det (DB) | Detects text regions using Differentiable Binarization |
| Text Line Classification | PP-LCNet | Determines text line orientation (0°/180°) |
| Text Recognition | PP-OCRv5_rec (SVTR) | Recognizes text using Scene Text Recognition with CTC |

### Model Architecture

- **Text Detection (DB Algorithm)**
  - Backbone: ResNet50_vd
  - Neck: Feature Pyramid Network (FPN)
  - Head: Differentiable Binarization with probability, threshold, and binary maps

- **Text Recognition (SVTR)**
  - Patch Embedding with positional encoding
  - 4-stage encoder with local and global mixing
  - CTC (Connectionist Temporal Classification) decoder
  - Vocabulary: 6,624 characters

## Installation

### Requirements

- Python 3.8+
- Windows/Linux/macOS

### Install Dependencies

```bash
pip install paddlepaddle>=2.5.0
pip install paddleocr>=3.0.0
pip install pillow>=9.0.0
pip install opencv-python>=4.5.0
```

## Quick Start

### Basic Usage

```python
from paddleocr import PaddleOCR

# Initialize OCR engine
ocr = PaddleOCR(
    use_doc_orientation_classify=False,
    use_doc_unwarping=False
)

# Perform OCR on an image
result = ocr.predict('invoice.png')

# Process results
for item in result:
    if 'rec_texts' in item:
        for i, text in enumerate(item['rec_texts']):
            score = item['rec_scores'][i]
            print(f"Text: {text}, Confidence: {score:.2%}")
```

### Draw Boxes Only (No Text on Image)

```python
import cv2
import numpy as np

def draw_boxes_only(image_path, boxes, output_path):
    img = cv2.imread(image_path)
    for box in boxes:
        pts = np.array(box).astype(np.int32)
        cv2.polylines(img, [pts], True, (0, 255, 0), 2)
    cv2.imwrite(output_path, img)
```

### Save Results to TXT

```python
def save_text_to_file(txts, scores, output_path):
    with open(output_path, 'w', encoding='utf-8') as f:
        for i, (txt, score) in enumerate(zip(txts, scores)):
            f.write(f"{i+1}. {txt} (Confidence: {score:.2%})\n")
```

## Project Structure

```
AI-INVOICE-OCR-ENGINE/
├── README.md
├── README_CN.md
├── LICENSE-MIT
├── setup.py
├── demo/
│   ├── invoice_ch.png           # Chinese invoice (input)
│   ├── invoice_en.png           # English invoice (input)
│   ├── result_invoice_cn.jpg    # Chinese result (output)
│   ├── result_invoice_en.jpg    # English result (output)
│   ├── 1.png ~ 7.png            # Architecture diagrams
│   └── *.puml                   # PlantUML source files
├── models/
│   └── pretrained/
│       └── weights/             # Model files (.onnx, .pdmodel, .pdiparams)
├── preprocess/
├── detect/
├── recognize/
├── data/
├── losses/
├── train/
└── utils/
```

## Performance

| Model | Size | Inference Time (CPU) | Accuracy |
|-------|------|---------------------|----------|
| PP-OCRv5_det | ~88MB | ~200ms/image | F1: 0.85+ |
| PP-OCRv5_rec | ~85MB | ~50ms/text line | Acc: 0.95+ |
| PP-LCNet (orientation) | ~7MB | ~10ms/image | Acc: 0.99+ |

*Times measured on Intel i7-10700 CPU

## Supported Document Types

- Invoices and receipts
- Financial statements
- Contracts and agreements
- ID cards and certificates
- General printed documents
- Mixed Chinese/English documents

## Author

**Jammy Lin**
Email: a0925281767s@gmail.com

## License

This project is licensed under the MIT License - see the [LICENSE-MIT](LICENSE-MIT) file for details.

## Acknowledgments

- [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) - The underlying OCR engine
- [PaddlePaddle](https://www.paddlepaddle.org.cn/) - Deep learning framework

## References

- PP-OCRv5: [PaddleOCR Documentation](https://paddlepaddle.github.io/PaddleOCR/)
- DB Algorithm: [Real-time Scene Text Detection with Differentiable Binarization](https://arxiv.org/abs/1911.08947)
- SVTR: [SVTR: Scene Text Recognition with a Single Visual Model](https://arxiv.org/abs/2205.00159)

---

## ⭐ Support This Project

If this project helps you, please give it a **Star**!

Your support is my motivation to keep improving!

[![GitHub stars](https://img.shields.io/github/stars/xup6jammy/AI-INVOICE-OCR-ENGINE?style=social)](https://github.com/xup6jammy/AI-INVOICE-OCR-ENGINE)

**Ways to support:**
- ⭐ Star this repository
- 🍴 Fork and contribute
- 🐛 Report issues and suggestions
- 📢 Share with others!
