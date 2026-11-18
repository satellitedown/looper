# Looper 0.2.1

A desktop app for coding with a local model. It runs entirely on your Mac: the model, the agent and your files never leave the machine.

## Requirements

- Apple Silicon Mac (M1 or newer), macOS 13.3 or newer
- 16 GB of memory recommended; 8 GB not supported
- About 9 GB of free disk space, most of it for the model
- No Apple Developer account is needed to run it, see below

## Install

1. Open the downloaded `Looper_0.2.1_aarch64.dmg`.
2. Drag **Looper** into **Applications**.
3. Open it. The app is not notarized, so macOS shows **"Looper" Not Opened**. Click **Done**.
4. Allow it once, either way:
   - **System Settings → Privacy & Security**, scroll to the bottom, **Open Anyway** next to the message about Looper; or
   - in Terminal: `xattr -dr com.apple.quarantine /Applications/Looper.app`, then open it again.
5. The first launch downloads the model, 6.6 GB in two files, and checks them, which took about half a minute on the test Mac. The window shows the progress; the model stays on disk, so this happens once. Updating from an earlier version downloads only the new drafter (0.92 GB) and deletes the one it replaces.
6. Pick a folder and describe a task. The app asks before it changes files unless you change that in the permission menu.

## What is inside

- **The model**: a 9B parameter model running through a self-contained `llama-server` build with Apple GPU kernels for its looped architecture.
- **The drafter**: a small second model that proposes the next seven tokens for the main model to check in one pass. The answers stay the main model's; they arrive faster.
- **The agent**: OMP 18.22.1, bundled, with a small tool set (read, grep, glob, edit, write, bash) and a system prompt written for a small model.
- **One conversation at a time**, each in its own thread with its own approvals, diffs and revert.

## New in 0.2.1

The same answers, 14% faster on the agent tasks than 0.2.0, with 0.6 GB less memory (2.7 GB instead of 3.3 after a long prompt, besides the weights):

- **The engine no longer copies the model's memory for every token of a pass.** To undo the tokens a pass rejects it kept a copy per token, 700 MB written per pass. It now keeps a few numbers per token and rebuilds what it needs.
- **Text taken from the prompt is undone the same way.** When part of a long draft was wrong, the accepted part used to be run again.
- **A request is read in fewer passes**: one instead of three, or two where that is cheaper for the GPU.
- **A new drafter**, trained on twice the text, with an output layer of its own over the 65,536 tokens the model actually writes instead of all 248,320.

## Speed on an M4 MacBook Air, 16 GB

| | 0.2.0 | 0.2.1 |
|---|---|---|
| Ten coding tasks through the agent, all the model's writing | 25.3 tokens/s | 29.0 tokens/s |
| New code | 29.8 tokens/s | 31.6 tokens/s |
| An explanation in prose | 11.4 tokens/s | 12.2 tokens/s |
| Rewriting a file with a small change | 62.0 tokens/s | 65.7 tokens/s |
| A file written through a tool call | 57.3 tokens/s | 59.5 tokens/s |
| A new chat starts responding after | about 1 s (15 s for the first chat after the app starts) | the same |

The two versions were measured in turns on the same machine, each run after it had rested, twice. The first row is the one that matches real use: thinking on, three to four minutes of continuous work. The others are short runs at temperature 0 with thinking off. The Air has no fan, and after half an hour of continuous work either version is 15 to 25% slower. 0.1.0, which had no drafter, ran the agent tasks at 9.1 tokens/s.

## The model

The model is published separately, with the numbers behind it and the details of what was changed:

- Full precision: https://huggingface.co/satellitedown/SenseNova-Loop-9B-abliterated
- GGUF (4-bit and 6-bit): https://huggingface.co/satellitedown/SenseNova-Loop-9B-abliterated-GGUF

It is a modified version of [SenseNova-Loop-9B](https://huggingface.co/sensenova/SenseNova-Loop-9B), Apache-2.0, with its refusals removed. It carries no guarantee about what it will and will not answer, and nothing filters its output.

## Licence

The app bundles OMP (MIT), llama.cpp with SenseNova Loop support (MIT) and a set of Rust and JavaScript libraries; their licences are in the app at `Contents/Resources/THIRD-PARTY-NOTICES.txt`.

## Checksum

```
842e94317926e9308890c25fb154bb02d8e1419b5fad332cc802c672eee0a67c  Looper_0.2.1_aarch64.dmg
```
