# Speech-to-Text API

This repository contains the code for a live, functional Speech-to-Text (STT) API. The API is designed to transcribe audio files into text using a powerful, free, and open-source model.

## 🚀 Project Overview

This API provides a cost-effective solution for transcribing audio notes. The solution leverages free, high-performance tools to provide a robust service without ongoing costs.

## ✨ Features

* **Fast Transcription:** Utilizes a free Google Colab T4 GPU for accelerated performance.

* **Multilingual Support:** The underlying Whisper model automatically detects and transcribes audio in multiple languages.

* **Easy to Deploy:** Can be run directly from Google Colab, making setup and demonstration straightforward.

## 💻 Technology Stack

* **Framework:** **FastAPI** (Python web framework)

* **AI Model:** **Whisper** by OpenAI via Hugging Face `transformers` library

* **Deployment:** **Google Colab**

* **Tunneling:** **ngrok**

## 🔧 How to Run

Follow these steps to set up and run the API from your Google Colab notebook, executing each code block in a separate cell.

### 1. Install Libraries

Run this command in the first cell to install the necessary libraries.

    !pip install transformers torch fastapi "uvicorn[standard]"
    
### 2. Install ngrok

Run this command in the second cell to install the `pyngrok` library.

    !pip install pyngrok


### 3. Authenticate with ngrok

Obtain your authentication token from the [ngrok dashboard](https://dashboard.ngrok.com/get-started/your-authtoken) and run the following in a new cell.

    from pyngrok import ngrok
    ngrok.set_auth_token("YOUR_AUTH_TOKEN_HERE")

### 4. Run the API

Execute the main script in a new cell. This will start the API server and print the public URL.

    import torch
    import uvicorn
    from fastapi import FastAPI, UploadFile, File
    from transformers import pipeline
    import nest_asyncio
    from pyngrok import ngrok
    
    # Use nest_asyncio to allow Uvicorn to run in a Colab environment
    nest_asyncio.apply()
    
    # --- API setup ---
    # app = FastAPI()
    
    # --- Load the Whisper model ---
    # Using a GPU if available
    device = "cuda:0" if torch.cuda.is_available() else "cpu"
    pipe = pipeline("automatic-speech-recognition", model="openai/whisper-base", device=device)
    
    # --- API endpoint for transcription ---
    @app.post("/transcribe/")
    async def transcribe_audio(file: UploadFile = File(...)):
    # Read the uploaded audio file
    audio_data = await file.read()
    
    # Transcribe the audio using the Whisper model
    transcription = pipe(audio_data)["text"]
    
    return {"transcription": transcription}
    # --- Start the API and create a public URL with ngrok ---
    # This part runs the API and generates a public URL
    ngrok_tunnel = ngrok.connect(8000)
    print(f'Public URL: {ngrok_tunnel.public_url}')
    
    uvicorn.run(app, port=8000)


## 🌐 API Endpoint

* **Endpoint:** `/transcribe/`

* **Method:** `POST`

* **Accepts:** `multipart/form-data` with an audio file (e.g., .mp3, .wav)

* **Returns:** A JSON object with the transcription:

      {
      "transcription": "The transcribed text will appear here."
      }


### How to Test the API

To verify that the API is working, you can use its built-in interactive documentation. Once the server is running, navigate to the public URL provided by ngrok and add `/docs` to the end.

**Example URL:** `https://[your_ngrok_url].ngrok-free.app/docs`

This will take you to the Swagger UI, where you can click on the `/transcribe/` endpoint, upload an audio file, and click "Execute" to test the transcription.

### Example Usage (using `curl`)

    curl -X POST "https://[your_ngrok_url].ngrok-free.app/transcribe/"
    
    -H "accept: application/json"
    
    -H "Content-Type: multipart/form-data"
    
    -F "file=@/path/to/your/audio.mp3"
