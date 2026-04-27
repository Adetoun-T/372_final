Attribution
AI Development Tools
Claude (Anthropic)
Claude was used throughout this project for code assistance and debugging. 
ChatGPT (OpenAI GPT-4)
Used to generate the fine-tuning training dataset (music_training_data.json).
Prompts used:
Generate 100 fine-tuning examples for a music commentary model. Each example should be: - Input: Track: [name], Artist: [name], Album: [name], Year: [year], Explicit: [true/false], Context: [album/playlist] - Output: A 1-2 sentence witty, slightly sarcastic comment about what this song choice says about the listener. The comment should reference real cultural associations with the artist or genre. Be specific — mention the artist's reputation, the album's cultural significance, or the genre's stereotypes. Vary tone between: affectionate roast, confused, impressed, concerned, nostalgic. Cover a wide range of genres and eras. Output only as JSON: {"input": "...", "output": "..."}
To make this more specific to my own musical taste. I got my top 50 songs for the past 4 weeks, the past 6 months and the past year and had ChatGPT generate witty responses to these inputs, as well as other popular songs.
What was modified: All generated examples were reviewed manually. All were edited for tone consistency, cultural accuracy, or formatting. About 10 were discarded and replaced with manually written examples where the generated output was too generic or factually inaccurate about the artist.
