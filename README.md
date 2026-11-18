# Looper 0.2.0

A desktop app for coding with a local model. It runs entirely on your Mac: the model, the agent and your files never leave the machine.

## Requirements

- Apple Silicon Mac (M1 or newer), macOS 13.3 or newer
- 16 GB of memory recommended; 8 GB not supported
- About 9 GB of free disk space, most of it for the model
- No Apple Developer account is needed to run it, see below

## Install

1. Open the downloaded `Looper_0.2.0_aarch64.dmg`.
2. Drag **Looper** into **Applications**.
3. Open it. The app is not notarized, so macOS shows **"Looper" Not Opened**. Click **Done**.
4. Allow it once, either way:
   - **System Settings → Privacy & Security**, scroll to the bottom, **Open Anyway** next to the message about Looper; or
   - in Terminal: `xattr -dr com.apple.quarantine /Applications/Looper.app`, then open it again.
5. The first launch downloads the model, 6.4 GB in two files, and checks them, which took about half a minute on the test Mac. The window shows the progress; the model stays on disk, so this happens once. Updating from 0.1.0 downloads only the new file, the drafter (0.77 GB).
6. Pick a folder and describe a task. The app asks before it changes files unless you change that in the permission menu.

## What is inside

- **The model**: a 9B parameter model running through a self-contained `llama-server` build with Apple GPU kernels for its looped architecture.
- **The drafter**, new in 0.2.0: a small second model that proposes the next seven tokens for the main model to check in one pass. The answers stay the main model's; they arrive faster. It takes about 1.4 GB more memory while the app runs.
- **The agent**: OMP 18.22.1, bundled, with a small tool set (read, grep, glob, edit, write, bash) and a system prompt written for a small model.
- **One conversation at a time**, each in its own thread with its own approvals, diffs and revert.

## Speed on an M4 MacBook Air, 16 GB

| | 0.1.0 | 0.2.0 |
|---|---|---|
| Ten coding tasks through the agent, all the model's writing | 9.1 tokens/s | 22.6 tokens/s |
| New code | 11.08 tokens/s | 29.56 tokens/s |
| An explanation in prose | 11.02 tokens/s | 11.41 tokens/s |
| Rewriting a file with a small change | 43.11 tokens/s | 61.08 tokens/s |
| A file written through a tool call | 31.52 tokens/s | 54.58 tokens/s |
| A new chat starts responding after | about 1 s (15 s for the first chat after the app starts) | the same |

The first row is the one that matches real use: thinking on, the Mac warm from continuous work. The others are short runs on a cool machine at temperature 0 with thinking off. The Air has no fan, and a run of several minutes is 15 to 25% slower than a short one in either version.

## The model

The model is published separately, with the numbers behind it and the details of what was changed:

- Full precision: https://huggingface.co/satellitedown/SenseNova-Loop-9B-abliterated
- GGUF (4-bit and 6-bit): https://huggingface.co/satellitedown/SenseNova-Loop-9B-abliterated-GGUF

It is a modified version of [SenseNova-Loop-9B](https://huggingface.co/sensenova/SenseNova-Loop-9B), Apache-2.0, with its refusals removed. It carries no guarantee about what it will and will not answer, and nothing filters its output.

## Licence

The app bundles OMP (MIT), llama.cpp with SenseNova Loop support (MIT) and a set of Rust and JavaScript libraries; their licences are in the app at `Contents/Resources/THIRD-PARTY-NOTICES.txt`.

## Checksum

```
f56b3800f821584abc5113d6288c3c50af9e420d3442379d7696319845d2637b  Looper_0.2.0_aarch64.dmg
```
