# PDF to Image with OCR and Layout Parsing

This Flask web application allows users to upload PDF files, which are then converted to images. The images undergo Optical Character Recognition (OCR) and layout parsing to extract text and visualize the document structure. This can be particularly useful for analyzing document layouts, extracting text data, and more.

## Features

- **PDF Upload**: Users can upload PDF files through a simple web interface.
- **Image Conversion**: Uploaded PDF files are converted into images for further processing.
- **OCR**: Optical Character Recognition is applied to extract text from images.
- **Layout Parsing**: Document layout is parsed to understand the structure of the content.
- **Visualization**: Processed images are displayed along with extracted text and annotated layout.

## Dependencies

- [Flask](https://flask.palletsprojects.com/): A lightweight web application framework.
- [pdf2image](https://pypi.org/project/pdf2image/): Converts PDF files into images.
- [layoutparser](https://github.com/Layout-Parser/layout-parser): Parses layout structure from images.
- [Tesseract OCR](https://github.com/tesseract-ocr/tesseract): Used for text extraction from images.
- [Detectron2](https://github.com/facebookresearch/detectron2): A powerful object detection library used for layout parsing.

## Setup and Installation

1. Clone this repository to your local machine:

    ```bash
    git clone https://github.com/your-username/pdf-to-image-ocr.git
    ```

2. Install the required dependencies:

    ```bash
    pip install -r requirements.txt
    ```

3. Run the Flask application:

    ```bash
    python app.py
    ```

4. Access the application through a web browser at `http://localhost:8000`.

## Usage

1. Navigate to the home page of the web application.
2. Click on the "Upload" button and select a PDF file.
3. Once the upload is complete, the images and extracted text will be displayed.
4. Explore the document layout and extracted text for each page.

## Google Colab Reference
For a detailed explanation and code implementation in a Google Colab notebook, you can refer to this [Google Colab Notebook](https://colab.research.google.com/drive/1174L2gDaSYgngXxWgCNerqzP-RtzJ8YX?usp=sharing).

## Code Explanation

### PDF Conversion and OCR

```python
from pdf2image import convert_from_path
from PIL import Image
import pytesseract

def extract_text_from_pdf(pdf_path):
    images = convert_from_path(pdf_path)
    text = ""
    for img in images:
        text += pytesseract.image_to_string(img)
    return text
```

This function `extract_text_from_pdf` converts each page of the PDF into an image and then uses Tesseract OCR to extract text from each image.

### Layout Parsing

```python
import layoutparser as lp

def parse_layout(image):
    model = lp.Detectron2LayoutModel('config.yaml', extra_config=["MODEL.ROI_HEADS.SCORE_THRESH_TEST", 0.4], label_map={0: "Text"})
    layout_result = model.detect(image)
    text_blocks = lp.Layout([b for b in layout_result])
    return text_blocks
```

This function `parse_layout` uses Detectron2 and layoutparser to parse the layout of the document image and extract text blocks.

### Flask App

```python
from flask import Flask, render_template, request, redirect, url_for

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/upload', methods=['POST'])
def upload_file():
    if 'file' not in request.files:
        return redirect(request.url)
    file = request.files['file']
    if file.filename == '':
        return redirect(request.url)
    if file:
        filename = file.filename
        # Perform OCR and layout parsing here
        return redirect(url_for('display_images', filename=filename))
    return redirect(request.url)

@app.route('/display/<filename>')
def display_images(filename):
    # Display processed images and text here
    return render_template('display.html')
```

This Flask app handles file uploads, performs OCR and layout parsing, and displays the processed images and text.

## Contributions

Contributions are welcome! If you have any suggestions, bug fixes, or feature implementations, feel free to open an issue or submit a pull request.

## License

This project is licensed under the [MIT License](LICENSE).

---
