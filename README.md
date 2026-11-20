# Project Recon

An MIT-licensed voice studio from Anthos Industries. Choose a preset or make a custom voice, generate a script in your browser, and download a 24 kHz mono WAV. [Open Project Recon](https://anthos-voice-studio.waheebginem.chatgpt.site/)

## Saved sounds

On the hosted studio, sign in with ChatGPT and choose **Save to cloud** after generating speech. Open **Sounds** to play, download, or remove the recordings saved to your account. Recent recordings and custom voice profiles also remain available on the device where you made them.

Cloud save sends the WAV and its displayed title, voice label, pace, and creation time. The title is derived from the beginning of the script. Voice reference clips stay in your browser. Cloud sounds are private to the signed-in account.

## Create a custom voice

Open **Custom voices** to choose a method, preview the voice, and save it. Use a saved voice in the Studio to generate a different script.

| Method | Engine | First-use model download | Script limit |
| --- | --- | --- | --- |
| Describe an accent, tone, and delivery | Qwen3-TTS VoiceDesign | About 4.3 GB | 500 characters |
| Blend two presets with an adjustable mix | Kokoro 82M | About 93 MB | 6,000 characters |
| Choose a recording of your own voice | Kyutai Pocket TTS | About 146 MB | 6,000 characters |

The description mode requires a desktop browser with a working WebGPU adapter and enough graphics memory for the model. The saved profile keeps the description and uses it for subsequent scripts. The blend mode interpolates the preset voice profiles before synthesizing speech. The first preset sets pronunciation for a mixed voice. The recording mode uses the first 10 seconds of the audio and requires at least 3 seconds of clear speech. The app stores the reference locally only when you save the voice.

## Run locally

This repository includes the complete source as a Git patch. Apply it from a fresh clone to restore the browser files, server code, icon, and bundled license notices, then serve the static files with Python 3.

```sh
git clone https://github.com/xenos-anthos/project-recon.git
cd project-recon
git apply project-recon-source.patch
python3 -m http.server 8000 --directory dist
```

Open [localhost:8000](http://localhost:8000/) in a modern browser with WebAssembly and module worker support. The browser-based studio works without a build step or API key and can be deployed from `dist/` on any static HTTPS host.

The first preview or generation downloads the selected model and its runtime data from jsDelivr or Hugging Face. Later requests reuse cached files where browser storage permits. History and saved voices use IndexedDB. Clear local recordings under **Sounds** and remove individual voice profiles under **Custom voices**.

Generation speed depends on your device. Kokoro presets and blends support the speed control.

## Hosted account storage

The hosted build runs `node scripts/build-worker.mjs` and deploys `dist/server/index.js` as a Sites Worker with a private R2 binding named `RECON_SOUNDS`. The managed Sites gateway supplies authenticated identity. Keep the Worker behind that gateway because its account authorization trusts the gateway-injected identity headers. For standalone static hosting, the browser studio and local library work independently of the cloud service.

## Source and licenses

The browser source is in `dist/`. The hosted audio API is in `worker/`. Project Recon code is released under the MIT license in `LICENSE`.

```sh
zip -X -q -r project-recon-source.zip README.md LICENSE .gitignore dist/index.html dist/studio.css dist/*.js dist/assets dist/engines scripts worker
```

- Kokoro JS and the ONNX weights are Apache-2.0. The browser downloads `kokoro-js@1.2.1` and the q8 variant of Kokoro 82M v1.0. [Kokoro source](https://github.com/hexgrad/kokoro) · [ONNX model](https://huggingface.co/onnx-community/Kokoro-82M-v1.0-ONNX)
- Qwen3-TTS VoiceDesign weights and the vendored Vox WebGPU runtime are Apache-2.0. The runtime license is in `dist/engines/design/LICENSE.txt`. [Qwen model](https://huggingface.co/Qwen/Qwen3-TTS-12Hz-1.7B-VoiceDesign) · [Vox source](https://github.com/dariumi/Vox/tree/4314095151428f029c5bac36916508a1166a7518)
- Kyutai Pocket TTS model weights use CC BY 4.0. The vendored Pocket TTS JavaScript runtime is MIT-licensed with its notice in `dist/engines/clone/LICENSE.txt`. [Kyutai model](https://huggingface.co/kyutai/pocket-tts) · [ONNX conversion](https://huggingface.co/vlapky/pocket-tts-onnx) · [JavaScript runtime](https://github.com/vlapky/pocket-tts-js)
- Upstream phonemization uses the phonemizer wrapper and eSpeak NG, which carry their own licenses. [Phonemizer](https://github.com/xenova/phonemizer.js) · [eSpeak NG license](https://github.com/espeak-ng/espeak-ng/blob/master/COPYING)
- Roboto Condensed and Instrument Sans are distributed with their SIL Open Font License notices in `dist/assets/`.
