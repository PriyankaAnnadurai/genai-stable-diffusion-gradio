


# 📸 PromptPix: AI Image Creator

**Designed and deployed a prototype app using the Stable Diffusion model integrated with Gradio UI.**  
Enabled real-time image generation from user prompts via Hugging Face APIs.  
Created a clean, interactive Gradio interface with labeled inputs and visual feedback.

## 💻 Code

```python
import os
import io
from PIL import Image
import base64
from dotenv import load_dotenv, find_dotenv
import requests
import json
import gradio as gr

# Load API key from environment variables
_ = load_dotenv(find_dotenv())
hf_api_key = os.environ['HF_API_KEY']

# Helper function to interact with Hugging Face API
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_TTI_BASE']):
    headers = {
        "Authorization": f"Bearer {hf_api_key}",
        "Content-Type": "application/json"
    }
    data = {"inputs": inputs}
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST", ENDPOINT_URL, headers=headers, data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))

# Convert base64-encoded string to PIL Image
def base64_to_pil(img_base64):
    base64_decoded = base64.b64decode(img_base64)
    byte_stream = io.BytesIO(base64_decoded)
    pil_image = Image.open(byte_stream)
    return pil_image

# Gradio generation function
def generate(prompt):
    output = get_completion(prompt)
    result_image = base64_to_pil(output["data"][0]["base64"])
    return result_image

# Gradio interface
gr.close_all()
demo = gr.Interface(
    fn=generate,
    inputs=[gr.Textbox(label="Your prompt")],
    outputs=[gr.Image(label="Result")],
    title="Image Generation with Stable Diffusion",
    description="Generate any image with Stable Diffusion",
    allow_flagging="never",
    examples=["the spirit of a tamagotchi wandering in the city of Vienna", "a mecha robot in a favela"]
)

# Launch Gradio app
demo.launch()
```

## OUTPUT:
![image](https://github.com/user-attachments/assets/2487fb7c-9f49-4efe-96ec-f719953000f3)


