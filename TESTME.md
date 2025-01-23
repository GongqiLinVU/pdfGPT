# **Part 1: Explanations of Functions in `api.py`**

The `api.py` module in the `pdfGPT` project facilitates interaction with PDF documents through natural language queries using GPT capabilities. Below is an explanation of each function in the module:

## **Function Explanations**

### 1. `download_pdf(url: str, output_path: str) -> None`
- **Description:** Downloads a PDF file from the specified URL and saves it to the given output path.

### 2. `pdf_to_text(pdf_path: str, pages: list) -> str`
- **Description:** Extracts text from the specified pages of a PDF file located at `pdf_path`.

### 3. `preprocess(text: str) -> str`
- **Description:** Cleans the extracted text by removing excessive whitespace and newline characters.

### 4. `text_to_chunks(text: str, chunk_size: int = 300, overlap: int = 50) -> list`
- **Description:** Divides the text into overlapping chunks of specified `chunk_size`, with each chunk overlapping the previous one by `overlap` characters.

### 5. `load_recommender(pdf_path: str) -> None`
- **Description:** Loads the PDF, extracts text, preprocesses it, converts it into chunks, and initializes the `SemanticSearch` recommender with these chunks.

### 6. `load_openai_key() -> str`
- **Description:** Loads the OpenAI API key from the environment variable `OPENAI_API_KEY`.

### 7. `generate_text(prompt: str, openAI_key: str) -> str`
- **Description:** Generates text using the OpenAI API based on the provided prompt and API key.

### 8. `generate_answer(question: str, openAI_key: str) -> str`
- **Description:** Generates an answer to the user's question by finding relevant text chunks from the PDF and using the OpenAI API to formulate a response.

### 9. `ask_url(url: str, question: str) -> str`
- **Description:** Downloads a PDF from the specified URL, processes it, and returns an answer to the user's question.

### 10. `ask_file(file: UploadFile, question: str) -> str`
- **Description:** Processes an uploaded PDF file and returns an answer to the user's question.

---

# **Part 2: Test Cases for `api.py`**

Below are the test cases designed to verify the functionality of each function in `api.py`.

## **Test Cases**

### 1. **`download_pdf`**
- **Input:**
  - URL: `"https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"`
  - Output Path: `"test_download.pdf"`
- **Expected Output:** File `"test_download.pdf"` exists after execution.

### 2. **`pdf_to_text`**
- **Input:**
  - PDF Path: `"test_download.pdf"`
  - Pages: `[0]`
- **Expected Output:** Text extracted from the first page (e.g., `"Dummy PDF file for testing purposes."`).

### 3. **`preprocess`**
- **Input:**
  - Text: `"  This is   a sample text. \n\n  With extra spaces.\n"`
- **Expected Output:** `"This is a sample text. With extra spaces."`

### 4. **`text_to_chunks`**
- **Input:**
  - Text: `"This is a long document that should be chunked for analysis."`
  - Chunk Size: `10`
  - Overlap: `2`
- **Expected Output:**
  - `["This is a ", "is a long ", "a long do", "long docu", "document "]`.

### 5. **`generate_text`**
- **Input:**
  - Prompt: `"What is the capital of France?"`
  - OpenAI API Key: `"test_key"`
- **Expected Output:** `"The capital of France is Paris."`

### 6. **`generate_answer`**
- **Input:**
  - Question: `"What is the purpose of this document?"`
  - OpenAI API Key: `"test_key"`
- **Expected Output:** `"The document provides dummy text for testing purposes."`

### 7. **`ask_url`**
- **Input:**
  - URL: `"https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"`
  - Question: `"What is this document about?"`
- **Expected Output:** `"This is a dummy PDF document for testing purposes."`

### 8. **`ask_file`**
- **Input:**
  - File: An uploaded PDF file named `"test.pdf"`
  - Question: `"What are the key points discussed?"`
- **Expected Output:** A valid string response summarizing key points.

---

# **Automation for Test Case Verification**

Below is the script to automate the execution of these test cases using `pytest`.

## **Script: `test_api.py`**

```python
import os
import pytest
from fastapi import UploadFile
from api import download_pdf, pdf_to_text, preprocess, text_to_chunks, generate_text, generate_answer, ask_url, ask_file

def test_download_pdf():
    url = "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
    output_path = "test_download.pdf"
    download_pdf(url, output_path)
    assert os.path.exists(output_path)
    os.remove(output_path)

def test_pdf_to_text():
    pdf_path = "test_download.pdf"
    pages = [0]
    download_pdf("https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf", pdf_path)
    text = pdf_to_text(pdf_path, pages)
    assert "Dummy PDF file" in text
    os.remove(pdf_path)

def test_preprocess():
    text = "  This is   a sample text. \n\n  With extra spaces.\n"
    cleaned_text = preprocess(text)
    assert cleaned_text == "This is a sample text. With extra spaces."

def test_text_to_chunks():
    text = "This is a long document that should be chunked for analysis."
    chunks = text_to_chunks(text, chunk_size=10, overlap=2)
    expected_chunks = ["This is a ", "is a long ", "a long do", "long docu", "document "]
    assert chunks == expected_chunks

def test_generate_text():
    prompt = "What is the capital of France?"
    openAI_key = "test_key"  # Replace with a valid key for a live test
    response = generate_text(prompt, openAI_key)
    assert response == "The capital of France is Paris."

def test_generate_answer():
    question = "What is the purpose of this document?"
    openAI_key = "test_key"  # Replace with a valid key for a live test
    answer = generate_answer(question, openAI_key)
    assert "testing purposes" in answer

def test_ask_url():
    url = "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
    question = "What is this document about?"
    answer = ask_url(url, question)
    assert "dummy PDF document" in answer

def test_ask_file():
    file = UploadFile(filename="test.pdf")
    question = "What are the key points discussed?"
    answer = ask_file(file, question)
    assert isinstance(answer, str) and len(answer) > 0

if __name__ == "__main__":
    pytest.main(["-v"])
```

---

