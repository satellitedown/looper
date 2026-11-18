# Looper 0.3.0

A desktop app for coding with a local model. It runs entirely on your Mac: the model, the agent and your files never leave the machine.

## Requirements

- Apple Silicon Mac (M1 or newer), macOS 13.3 or newer
- 16 GB of memory recommended; 8 GB not supported
- About 9 GB of free disk space, most of it for the model
- No Apple Developer account is needed to run it, see below

## Install

1. Open the downloaded `Looper_0.3.0_aarch64.dmg`.
2. Drag **Looper** into **Applications**.
3. Open it. The app is not notarized, so macOS shows **"Looper" Not Opened**. Click **Done**.
4. Allow it once, either way:
   - **System Settings → Privacy & Security**, scroll to the bottom, **Open Anyway** next to the message about Looper; or
   - in Terminal: `xattr -dr com.apple.quarantine /Applications/Looper.app`, then open it again.
5. The first launch downloads the model, 6.4 GB in two files, and checks them, which took about half a minute on the test Mac. The window shows the progress; the model stays on disk, so this happens once. Updating from 0.2.1 or earlier downloads it too, because this version runs a different model; the old one (6.6 GB) is deleted when the download starts, so the two never have to fit on the disk together.
6. Pick a folder and describe a task. The app asks before it changes files unless you change that in the permission menu.

## What is inside

- **The model**: Qwen3.5-9B with its refusals ablated, as a 4-bit file, running through a self-contained `llama-server` build with Apple GPU kernels.
- **The drafter**: a small second model that proposes the next seven tokens for the main model to check in one pass. The answers stay the main model's; they arrive faster.
- **The agent**: OMP 18.22.1, bundled, with a small tool set (read, grep, glob, edit, write, bash) and a system prompt written for a small model.
- **One conversation at a time**, each in its own thread with its own approvals, diffs and revert.

## New in 0.3.0: another model

Up to 0.2.1 the app ran a looped variant of Qwen3.5-9B that we had abliterated ourselves. In use it could think in circles: the same few thoughts, over and over, until the reply ran out of tokens. This version runs plain Qwen3.5-9B instead, in Abliterant's [RANA](https://huggingface.co/abliterant/Qwen3.5-9B-RANA-abliterated) abliteration, and the engine ends any thinking after 1,024 tokens.

- **It gets stuck thinking less.** On 160 generated coding tasks run through the app's agent, the old model wrote until the reply's token limit in 9 turns and ran out of time on 16 tasks. This one did so in 1 turn and on 12 tasks. A judge reading the transcripts counted 108 tasks done, 40 partly done and 12 failed, against 105, 35 and 20. That is one run each, so a few tasks either way are chance. It is no better at not repeating itself in what it does: in 16 conversations it issued the same tool call three times or more, against 12.
- **It reads faster.** A prompt token takes 7.7 ms in the agent tasks on the test Mac, against 13.2: the old model ran most of its layers twice.
- **The cap on thinking is what makes this model answer.** RANA removes the refusals but not all of the hesitation. Given 100 requests of the kind the original model refuses, it left 67 without an answer when it was free to think on: it went back and forth between the same few sentences until the tokens ran out. With the cap it answers all 100: 64 carried out, 29 dodged, 7 refused, as labelled by a judge model. With thinking switched off it is more cautious: 30 carried out, 53 dodged, 17 refused. On the coding tasks the cap changed nothing we could measure (109 done, 35 partly and 16 failed without it).
- **The drafter is not tuned to this model yet.** It is z-lab's public drafter for Qwen3.5-9B, trained on the original model's text. In the agent tasks 35% of the tokens it proposed were accepted, where the drafter we trained for the old model reached 76%. Drafting still changes nothing in the answers: all 32 test answers were token for token the same with and without it.

## Speed on an M4 MacBook Air, 16 GB

| | 0.2.1 | 0.3.0 |
|---|---|---|
| Ten coding tasks through the agent, all the model's writing | 29.0 tokens/s | 29.9 tokens/s |
| Reading the prompts of those tasks | 13.2 ms per token | 7.7 ms per token |
| New code | 31.6 tokens/s | 48.2 tokens/s |
| An explanation in prose | 12.2 tokens/s | 24.6 tokens/s |
| Rewriting a file with a small change | 65.7 tokens/s | 103.3 tokens/s |
| A file written through a tool call | 59.5 tokens/s | 84.0 tokens/s |
| The same four without the drafter | 10.9 tokens/s | 18.3 tokens/s |
| A new chat starts responding after | about 1 s (15 s for the first chat after the app starts) | under 1 s (8 s for the first chat) |
| Memory after a long prompt, besides the weights | 2.7 GB | 2.2 GB |

Both columns were measured on the same machine on the same day, each run after it had rested, twice; the two versions were not run in turns. The first two rows are the ones that match real use: thinking on, about three minutes of continuous work, and the tasks passed 10/10 both times. The speed rows below them are short runs at temperature 0 with thinking off. The Air has no fan, and after half an hour of continuous work either version is slower.

## The model

Neither file is ours; the app downloads both from their publishers and checks them against a fixed checksum:

- Weights: `Qwen3.5-9B-RANA-abliterated-Q4_K_M.gguf` from [abliterant/Qwen3.5-9B-RANA-abliterated-GGUF](https://huggingface.co/abliterant/Qwen3.5-9B-RANA-abliterated-GGUF), which describes the method and its own evaluation. It is [Qwen3.5-9B](https://huggingface.co/Qwen/Qwen3.5-9B) with an edit to its weights, published as a research model.
- Drafter: `qwen35-9b-dflash-Q4_K_M.gguf` from [Anbeeld/Qwen3.5-9B-DFlash-GGUF](https://huggingface.co/Anbeeld/Qwen3.5-9B-DFlash-GGUF), a 4-bit file of [z-lab/Qwen3.5-9B-DFlash](https://huggingface.co/z-lab/Qwen3.5-9B-DFlash).

Qwen3.5-9B, the RANA model and z-lab's drafter are Apache-2.0. The model carries no guarantee about what it will and will not answer, and nothing filters its output.

The model the app ran up to 0.2.1 stays published at [satellitedown/SenseNova-Loop-9B-abliterated](https://huggingface.co/satellitedown/SenseNova-Loop-9B-abliterated), and 0.2.1 stays available for it under Releases.

## Licence

The app bundles OMP (MIT), llama.cpp with SenseNova Loop support (MIT) and a set of Rust and JavaScript libraries; their licences are in the app at `Contents/Resources/THIRD-PARTY-NOTICES.txt`.

## Checksum

```
d47bff96aebaadca0f9b73b1fc4747b1effc78aa419863987b48e1528891573e  Looper_0.3.0_aarch64.dmg
```
