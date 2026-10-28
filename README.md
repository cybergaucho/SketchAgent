
# SketchAgent: Language-Driven Sequential Sketch Generation

<a href="https://yael-vinker.github.io/sketch-agent/"><img src="https://img.shields.io/static/v1?label=Project&message=Website&color=blue"></a>
<a href="https://arxiv.org/abs/2411.17673"><img src="https://img.shields.io/badge/arXiv-2311.13608-b31b1b.svg"></a>
<a href="https://www.apache.org/licenses/LICENSE-2.0.txt"><img src="https://img.shields.io/badge/License-Apache-yellow"></a>

<br>
<p align="center">
<img src="repo_images/teaser.jpg" width="90%"/>  
  
> <a href="">**SketchAgent: Language-Driven Sequential Sketch Generation**</a>
>
<a href="https://yael-vinker.github.io/website/" target="_blank">Yael Vinker</a>,
<a href="https://tamarott.github.io/" target="_blank">Tamar Rott Shaham</a>,
<a href="https://kristinezheng.github.io/" target="_blank">Kristine Zheng</a>,
<a href="https://www.linkedin.com/in/alex-zhao-a28b12176/" target="_blank">Alex Zhao</a>,
<a href="https://profiles.stanford.edu/judith-fan" target="_blank">Judith E Fan</a>,
<a href="https://groups.csail.mit.edu/vision/torralbalab/" target="_blank">Antonio Torralba</a>

> <br>
>  SketchAgent leverages an off-the-shelf multimodal LLM to facilitate language-driven, sequential sketch generation through an intuitive sketching language. It can sketch diverse concepts, engage in interactive sketching with humans, and edit content via chat.
</p>

**Fork note:** This is a fork of the original repo with multi-provider support added to `gen_sketch.py` (Anthropic, OpenAI, or OpenRouter). Ideal for free testing with OpenRouter's free vision models. The collaborative (`collab_sketch.py`) and chat-edit interfaces still use Anthropic by default and would need similar adaptation for other providers.

## Setup
Clone the repository and navigate to the project folder:
```
git clone https://github.com/cybergaucho/SketchAgent.git
cd SketchAgent
```
Set up the environment:
```
conda env create -f environment.yml
conda activate sketch_agent
```

**For Mac users**, use the following environment file instead:
```
conda env create -f mac_environment.yml
conda activate sketch_agent
```
If python flashes a warning at you, try reinstalling `cairosvg`:
```
conda uninstall cairosvg && conda install cairosvg
```

#### API Keys
`gen_sketch.py` supports multiple providers via the `--provider` flag.

**Recommended for free testing: OpenRouter**
1. Sign up at [openrouter.ai](https://openrouter.ai) (no credit card required for free tier).
2. Create an API key in the dashboard.
3. Add to your `.env` file:
```
OPENROUTER_API_KEY=sk-or-v1-...
```

Free tier limits: ~50 requests/day (20/min). Rises to 1,000/day after a one-time $10 credit purchase. Free models end in `:free`.

**Vision-capable free models** (as of late Oct 2026; list changes — check openrouter.ai):
- `google/gemma-4-31b-it:free` (262K context, image/text/video) — good default
- `google/gemma-4-26b-a4b-it:free` (similar, smaller active params)
- `thinkingmachines/inkling:free` or `inkling-small:free` (1M+ context, image/text/audio)
- `nvidia/nemotron-3.5-super-vl:free` (1M context, image/text/video)
- `nvidia/nemotron-3-nano-omni-30b-a3b-reasoning:free` (256K, multi-modal)
- `dots-studio/dots-3-note-preview:free` (512K, image/text)
- `openrouter/free` (auto-router that picks a suitable free model, including vision when needed)

For Anthropic (original):
```
ANTHROPIC_API_KEY=...
```
For OpenAI:
```
OPENAI_API_KEY=...
```

# Start Sketching! :woman_artist: :art:
## Playground (easiest way to test)
A simple web UI where you enter a concept and instantly see the sketch:
```
python playground.py
```
Open http://127.0.0.1:5000 in your browser. Defaults to OpenRouter free models. Results appear inline with download links for PNG/SVG. Saved under `results/playground/`.

## Text-to-Sketch (CLI)
Generate a single sketch by running:
```
python gen_sketch.py --concept_to_draw "<your_concept_here>" --provider openrouter --model google/gemma-4-31b-it:free
```
For example:
```
python gen_sketch.py --concept_to_draw "sailboat" --provider openrouter --model google/gemma-4-31b-it:free
```

If you leave `--model` as the default Claude name and use `--provider openrouter`, it auto-falls back to the Gemma free model.

Optional arguments:
* ```--provider``` `anthropic` (default), `openai`, or `openrouter`
* ```--model``` Specific model ID (must support vision for best results with the grid image)
* ```--seed_mode``` Default is ```"deterministic"``` for reproducible results. Set to ```"stochastic"``` for increased variability.
* ```--path2save``` By default, results are saved to ```results/test/```.

## Collaborative Sketching
Collaborate with SketchAgent by alternating strokes! 
To use the interactive interface (still Anthropic-only in this fork):
```
python collab_sketch.py
```
This will launch a Flask-based web application. Once running, look for the following output in the terminal:
```
Server running at: http://<your-ip-address>:5000
```
Open the provided URL in your web browser to interact with the application. Results are saved to ```results/collab_sketching/```.
Use the text box to change the concept to be drawn.

## Chat-Based Editing

<br>
<p align="center">
<img src="repo_images/interface.png" width="90%"/>  

Interact with SketchAgent through natural language to edit existing sketches! To use the chat-based editing interface (Anthropic-only):

```bash
python chat_and_edit.py
```

This will launch a Flask-based web application. Once running, look for output like:

```
Server running at: http://<your-ip-address>:5000
```

You can then:

* Give **textual instructions** to edit specific sketch elements
* Add new elements through **natural conversation**

Results are saved to:

```
results/api_{timestamp}_{session_id}
```

---

### 👨‍💻 Accessing the Interface

#### ✅ If Running Locally:

Open the printed URL (e.g., `http://127.0.0.1:5000`) in your browser.

#### 🔐 If Running Remotely via SSH:

Use **SSH port forwarding** to access the app from your local browser:

1. On your local machine (not the server), forward the port:

   ```bash
   ssh -L 5000:localhost:5000 your_username@remote_server_ip
   ```

2. Once connected and the app is running, open:

   ```
   http://localhost:5000
   ```

   in your local browser. This securely tunnels traffic to the remote Flask app.




## Tips:
* The ```gen_sketch.py``` script produces sketches with variability. Try running it multiple times to explore different outcomes. Free models may need a few tries or clearer prompts.
* Prompts are available in the ```prompts.py file```. For unique concepts, ensure that your input prompt is clear and meaningful.
* Vision support is important because the system can optionally send a grid canvas image. Free multimodal models work best.
* Rate limits on free OpenRouter models are low — perfect for testing sketches, not production.
* The playground is the quickest way to experiment with different concepts for learning illustrations.

## TODOs

- [ ] Add support for chat based editing.
- [ ] Add SVG drawing process animations in HTML.
- [x] Add support of other backbone models (OpenAI, OpenRouter free vision models).
- [x] Simple playground web UI for testing prompts.
- [ ] Port multi-provider support to collab_sketch.py and chat_and_edit.py.

## Citation
If you find this useful for your research, please cite the following:
```bibtex
@misc{vinker2024sketchagent,
      title={SketchAgent: Language-Driven Sequential Sketch Generation}, 
      author={Yael Vinker and Tamar Rott Shaham and Kristine Zheng and Alex Zhao and Judith E Fan and Antonio Torralba},
      year={2024},
      eprint={2411.17673},
      archivePrefix={arXiv},
      primaryClass={cs.CV},
      url={https://arxiv.org/abs/2411.17673}, 
}
```
