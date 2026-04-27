# MusicCritic

## What It Does

MusicCritic is a locally-run web application that connects to your Spotify account via a Model Context Protocol (MCP) 
server and uses a fine-tuned language model to generate witty, sarcastic commentary about whatever you're currently listening to.
When you play a track, the app fetches metadata from Spotify (track name, artist, album, year, and whether it's explicit) , passes it to a 
locally-hosted fine-tuned LLM, and returns a “roast” of your music taste. The model was fine-tuned from Llama 3.2 3B-Instruct on a custom 
dataset of 120 music commentary examples using LoRA adapters, adapting a general-purpose instruction-following model to a specialized creative commentary domain.

## Quick Start
You need Node.js, Python with uvx, and Ollama installed locally.
git clone https://github.com/Adetoun-T/372_final
cd music-roast
npm install
cp .env.example .env

Fill in your Spotify credentials in .env, then:
# Terminal 1
ollama serve

# Terminal 2
uvx mcpo --port 8000 --config ./mcp_config.json

# Terminal 3
npm run dev

Open http://localhost:5173, authorize Spotify, and play a track.
See SETUP.md for full installation instructions.

## Video Links
Demo video: []

Technical walkthrough: https://youtu.be/qjGwEOZDJYA

## Evaluation
The model was evaluated both quantitatively and qualitatively.
Quantitative results :
Metric
Value
Initial training loss
3.534069
Final training loss
0.705962
Final validation loss
1.741213

Training loss decreased by 80% over 20 epochs, confirming the model learned the target commentary style. Validation loss diverged from training loss after approximately epoch 10, indicating overfitting on the small training set.
Hyperparameter tuning results:
Run
Learning Rate
LoRA Rank
Epochs
Final Train Loss
Final Val Loss
1
2e-4
8
20
0.7060
1.7412
2
1e-4
8
20
1.2393         
1.5630
3
2e-4
16
20
0.7092
1.7231

Although Run 2 achieved lower validation loss, I selected Run 1 for the final model because the lower validation loss in Run 2 came at the cost of higher training loss. This suggests the model learned the target style less thoroughly. And for my creative commentary task, qualitative output quality matters more than generalization metrics and Run 1's outputs were noticeably more “witty” and culturally specific. So I accepted some overfitting because the goal is style imitation rather than generalization.
### Qualitative results:
Before fine-tuning, the base model produced generic, flat responses that restated the input metadata. After fine-tuning, the model produces culturally-specific, personality-driven commentary that roasts the listener rather than describing the song.
Before: "Hotline Bling is a popular song by Drake. It has themes about communication and relationships."
After: "Drake 'Hotline Bling'?? You're committing to this like it's a lifestyle choice. Who are you pretending to be again?"

### Model improvement iterations:
Iteration 1: Initial training used a 90/10 train/eval split. I observed val loss diverging significantly from train loss (1.72 vs 0.69), proof of overfitting. However, the outputs remained strong and witty, suggesting the model learned the target style without fully memorizing training data. I decided to keep 20 epochs (overtrained model) given the satisfactory qualitative performance.
Iteration 2: I restructured to a proper 80/10/10 train/val/test split to establish a clean held-out test set for final evaluation. I reran training and confirmed the val loss pattern was consistent with the original run.

### Design Decisions
Why LoRA instead of full fine-tuning: Fully fine-tuning of a 3B parameter model requires updating all (about) 3.2 billion parameters, which is more than the available GPU memory on Colab's free T4 (16GB). LoRA freezes the original weights and trains only 2.3M adapter parameters (only 0.07% of total), making fine-tuning feasible with minimal performance tradeoff. 
Why Llama 3.2 3B instead of a larger model: Larger models (7B+) would not fit in T4 memory even with LoRA without 4-bit quantization, which introduces additional complexity. 3B is the largest model that runs stably on free Colab hardware. The model is large enough to internalize cultural associations about artists and genres from pretraining.
Why local inference instead of API (GPT-4, Claude): Local inference via Ollama keeps the model cheap by eliminating per-token API costs. I was working on this project in another class and opted for fine tuning a free model rather than using a closed API model, because I had more control with customization.
