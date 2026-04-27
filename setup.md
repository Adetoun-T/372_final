Setup
Prerequisites
Node.js 18+ and npm
Python 3.10+ with uvx (pip install uvx)
Ollama installed and running
A Spotify Developer account with an app created at developer.spotify.com

Step 1: Clone the repository
git clone https://github.com/Adetoun-T/372_final
cd music-roast
npm install


Step 2: Set up the Spotify MCP server
git clone https://github.com/sespinosa/spotify-mcp.git
cd spotify-mcp
npm install
npm run build
cd ..


Step 3: Configure mcpo
Create mcp_config.json in the project root (do not commit this file):
{
  "mcpServers": {
    "spotify": {
      "command": "node",
      "args": ["/absolute/path/to/spotify-mcp/dist/index.js"],
      "env": {
        "SPOTIFY_CLIENT_ID": "your_client_id",
        "SPOTIFY_CLIENT_SECRET": "your_client_secret",
        "SPOTIFY_REDIRECT_URI": "http://127.0.0.1:8888/callback",
        "SPOTIFY_PERSIST_TOKENS": "true"
      }
    }
  }
}

Replace /absolute/path/to/spotify-mcp with the actual path on your machine.

Step 4: Configure environment variables
cp .env.example .env

Open .env and set:
ollama_model=llama3.2:3b


Step 5: Add redirect URI to Spotify Dashboard
In your Spotify Developer app settings at developer.spotify.com, add the following as an allowed redirect URI:
http://127.0.0.1:8888/callback


Step 6: Pull the Ollama model
ollama pull llama3.2:3b


Running the App
Open three terminals and run each of the following:
Terminal 1 — Ollama:
ollama serve

Terminal 2 — mcpo:
uvx mcpo --port 8000 --config ./mcp_config.json

Terminal 3 — SvelteKit:
npm run dev

Open http://localhost:5173 in your browser.

Spotify Authorization
Click Get Auth URL in the app
Open the URL in your browser and authorize the app
After authorizing, copy the full callback URL from your browser's address bar
Paste it into the app and click Complete Auth
You're now connected — click What am I listening to? to get your first roast

Fine-Tuning (Optional)
To reproduce the fine-tuned model:
Open 372_Final.ipynb in Google Colab
Switch runtime to T4 GPU (Runtime → Change runtime type → T4 GPU)
Upload music_training_data.json to your Google Drive
Run all cells in order
Export the trained model to GGUF format and load into Ollama via a Modelfile

Troubleshooting
mcpo port conflict: If port 8000 is in use, change --port 8000 to another port and update the corresponding fetch URL in src/routes/+page.svelte.
Ollama not found: Make sure Ollama is installed and ollama serve is running before starting mcpo or the SvelteKit app.
Spotify auth callback mismatch: Make sure the redirect URI in mcp_config.json exactly matches what's registered in your Spotify Developer dashboard.
Colab GPU OOM: If training fails with an out-of-memory error, reduce per_device_train_batch_size from 4 to 2 in the TrainingArguments cell.
