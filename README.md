# Looper 0.1.0

A desktop app for coding with a local model. It runs entirely on your Mac: the model, the agent and your files never leave the machine.

## Requirements

- Apple Silicon Mac (M1 or newer), macOS 13.3 or newer
- 16 GB of memory recommended; 8 GB not supported
- About 8 GB of free disk space, most of it for the model weights
- No Apple Developer account is needed to run it, see below

## Install

1. Open the downloaded `Looper_0.1.0_aarch64.dmg`.
2. Drag **Looper** into **Applications**.
3. Open it. The app is not notarized, so macOS shows **"Looper" Not Opened**. Click **Done**.
4. Allow it once, either way:
   - **System Settings → Privacy & Security**, scroll to the bottom, **Open Anyway** next to the message about Looper; or
   - in Terminal: `xattr -dr com.apple.quarantine /Applications/Looper.app`, then open it again.
5. The first launch downloads 5.6 GB of model weights, then checks the file, which took about half a minute on the test Mac. The window shows the progress; the model stays on disk, so this happens once.
6. Pick a folder and describe a task. The app asks before it changes files unless you change that in the permission menu.

## What is inside

- **The model**: a 9B parameter model running through a self-contained `llama-server` build with Apple GPU kernels for its looped architecture.
- **The agent**: OMP 18.22.1, bundled, with a small tool set (read, grep, glob, edit, write, bash) and a system prompt written for a small model.
- **One conversation at a time**, each in its own thread with its own approvals, diffs and revert.

## Speed on an M4 MacBook Air, 16 GB

| | |
|---|---|
| Writing new text | 11.11 tokens/s |
| Rewriting a file with a small change | 43.11 tokens/s |
| A file written through a tool call | 31.52 tokens/s |
| A new chat starts responding after | about 1 s (15 s for the first chat after the app starts) |

These are short-run numbers. The Air has no fan: under continuous work new text dropped to about 9 tokens/s within minutes and to about 7 after twenty-five.

## The model

The model is published separately, with the numbers behind it and the details of what was changed:

- Full precision: https://huggingface.co/satellitedown/SenseNova-Loop-9B-abliterated
- GGUF (4-bit and 6-bit): https://huggingface.co/satellitedown/SenseNova-Loop-9B-abliterated-GGUF

It is a modified version of [SenseNova-Loop-9B](https://huggingface.co/sensenova/SenseNova-Loop-9B), Apache-2.0, with its refusals removed. It carries no guarantee about what it will and will not answer, and nothing filters its output.

## Licence

The app bundles OMP (MIT), llama.cpp with SenseNova Loop support (MIT) and a set of Rust and JavaScript libraries; their licences are in the app at `Contents/Resources/THIRD-PARTY-NOTICES.txt`.

## Checksum

```
e3f85899f3ed76473513553bc64e97a05148076ec106a9727504bd4031a55e64  Looper_0.1.0_aarch64.dmg
```
